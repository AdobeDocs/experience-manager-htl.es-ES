---
title: Lenguaje de expresión HTL
description: Obtenga información acerca del uso del lenguaje de expresión HTL en AEM. El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML.
exl-id: 57e3961b-8c84-4d56-a049-597c7b277448
source-git-commit: 7b53eff0652f650ffb8caae0e69aa349b5c548eb
workflow-type: ht
source-wordcount: '1860'
ht-degree: 100%

---

# Lenguaje de expresión HTL {#htl-expression-language}

El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML. Estas expresiones están delimitadas por caracteres `${` y `}`. Para evitar el formato incorrecto de HTML, las expresiones solo se pueden utilizar en valores de atributo, en contenido de elemento o en comentarios.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Las expresiones se pueden escapar anteponiendo un carácter `\`, por ejemplo, `\${test}` renderizará `${test}`.

>[!NOTE]
>
>Para probar los ejemplos proporcionados en esta página, se puede utilizar un entorno de ejecución en directo denominado [Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl).

La sintaxis de la expresión incluye [variables](#variables), [literales](#literals), [operadores](#operators) y [opciones](#options):

## Variables {#variables}

Las variables son contenedores que almacenan valores u objetos de datos. Los nombres de las variables se denominan identificadores.

Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que normalmente estaban disponibles en JSP después de incluir `global.jsp`. La página [Objetos globales](global-objects.md) proporciona la lista de todos los objetos a los que HTL proporciona acceso.

### Acceso a propiedades {#property-access}

Existen dos formas de acceder a las propiedades de las variables, con notación de puntos o con notación de corchetes:

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

En la mayoría de los casos, se debe preferir la notación de puntos más sencilla, y la notación de corchetes debe utilizarse para acceder a propiedades que contienen caracteres de identificador no válidos o para acceder a propiedades de forma dinámica. Los dos capítulos siguientes proporcionarán detalles sobre estos dos casos.

Las propiedades a las que se accede pueden ser funciones, aunque no se admiten argumentos de paso, por lo que solo se puede acceder a las funciones que no esperan argumentos, como getters. Se trata de una limitación deseada, que pretende reducir la cantidad de lógica incrustada en las expresiones. Si es necesario, se puede utilizar la instrucción [`data-sly-use`](block-statements.md#use) para pasar parámetros a la lógica.

En el ejemplo anterior también se muestra que se puede acceder a las funciones de captador de Java, como `getTitle()`, sin anteponer el `get`, y reduciendo las mayúsculas y minúsculas del carácter que sigue.

### Caracteres de identificador válidos {#valid-identifier-characters}

Los nombres de las variables, llamados identificadores, se ajustan a determinadas reglas. Deben comenzar con una letra (`A`-`Z` y `a`-`z`) o un guión bajo (`_`), y los caracteres posteriores también pueden ser dígitos (`0`-`9`) o dos puntos (`:`). Las letras Unicode como `å` y `ü` no se pueden usar en identificadores.

Dado que el carácter de dos puntos (`:`) es común en los nombres de propiedades de AEM, debe destacarse que es convenientemente un carácter identificador válido:

`${properties.jcr:title}`

La notación de corchetes se puede utilizar para acceder a propiedades que contienen caracteres de identificador no válidos, como el carácter de espacio del ejemplo siguiente:

`${properties['my property']}`

### Acceso dinámico a los miembros {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Gestión permisiva de valores nulos {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## Literales {#literals}

Un literal es una anotación que representa un valor fijo.

### Booleano {#boolean}

Un booleano representa una entidad lógica y puede tener dos valores: `true` y `false`.

`${true} ${false}`

### Números {#numbers}

Solo hay un tipo de número: enteros positivos. Mientras que otros formatos de número, como coma flotante, son compatibles con las variables, pero no se pueden expresar como literales.

`${42}`

### Cadenas {#strings}

Las cadenas representan datos de texto y pueden estar entre comillas simples o dobles:

`${'foo'} ${"bar"}`

Además de los caracteres normales, se pueden utilizar los siguientes caracteres especiales:

* `\\` Carácter de barra invertida
* `\'` Comilla simple (o apóstrofo)
* `\"` Comilla doble
* `\t` Ficha
* `\n` Nueva línea
* `\r` Retorno de carro
* `\f` Avance de página
* `\b` Retroceso
* `\uXXXX` El carácter Unicode especificado por los cuatro dígitos hexadecimales XXXX.\
   Algunas secuencias de escape Unicode útiles son:

   * `\u0022` para `"`
   * `\u0027` para `'`

En el caso de los caracteres que no aparecen en la lista anterior, si precede a un carácter de barra invertida, se mostrará un error.

A continuación se muestran algunos ejemplos de cómo utilizar el escape de cadenas:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

que resultará en la siguiente salida, ya que HTL aplicará secuencias de escape específicas del contexto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Matrices {#arrays}

Una matriz es un conjunto ordenado de valores a los que se puede hacer referencia con un nombre y un índice. Los tipos de sus elementos se pueden mezclar.

```xml
${[1,2,3,4]}
${myArray[2]}
```

Las matrices son útiles para proporcionar una lista de valores de la plantilla.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operadores {#operators}

### Operadores lógicos {#logical-operators}

Estos operadores se utilizan normalmente con valores booleanos, pero, como en JavaScript, devuelven el valor de uno de los operandos especificados, por lo que, cuando se utilizan con valores no booleanos, pueden devolver un valor no booleano.

Si un valor se puede convertir a `true`, el valor se denomina true. Si un valor se puede convertir en `false`, el valor se denomina false. Los valores que se pueden convertir a `false` son variables no definidas, valores nulos, el número cero y cadenas vacías.

#### NOT lógico {#logical-not}

`${!myVar}` devuelve `false` si su operando único se puede convertir en `true`; de lo contrario, devuelve `true`.

Esto se puede utilizar, por ejemplo, para invertir una condición de prueba, como mostrar un elemento solo si no hay páginas secundarias:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND lógico {#logical-and}

`${varOne && varTwo}` devuelve `varOne` si es false; de lo contrario, devuelve `varTwo`.

Este operador se puede utilizar para probar dos condiciones a la vez, como verificar la existencia de dos propiedades:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

El operador lógico AND también se puede usar para mostrar atributos HTML de forma condicional, ya que HTL elimina los atributos con valores configurados dinámicamente como false o con una cadena vacía. Por lo tanto, en el ejemplo siguiente, el atributo `class` solo se muestra si `logic.showClass` es true y si `logic.className` existe y no está vacío:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR lógico {#logical-or}

`${varOne || varTwo}` devuelve `varOne` si es true; de lo contrario, devuelve `varTwo`.

Este operador puede utilizarse para comprobar si se aplica una de las dos condiciones, como verificar la existencia de al menos una propiedad:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Como el operador lógico OR devuelve la primera variable que es verdadera, también se puede utilizar muy convenientemente para proporcionar valores de reserva.

También se puede utilizar para mostrar de forma condicional los atributos HTML, ya que HTL elimina los atributos con valores establecidos por expresiones que se evalúan como false o como una cadena vacía. Por lo tanto, el ejemplo siguiente mostrará el título **`properties.jcr:`** si existe y no está vacío; de lo contrario, vuelve a mostrar **`properties.jcr:description`** si existe y no está vacío; de lo contrario, mostrará el mensaje “no se ha proporcionado título ni descripción”:

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operador condicional (ternario) {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` devuelve `varOne` si `varCondition` es true; de lo contrario, devuelve `varTwo`.

Este operador suele utilizarse para definir condiciones dentro de las expresiones, como mostrar un mensaje diferente en función del estado de la página:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>Dado que los caracteres de dos puntos también están permitidos en los identificadores, es mejor separar los operadores externos con un espacio en blanco para proporcionar claridad al analizador:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operadores de comparación {#comparison-operators}

Los operadores de igualdad y desigualdad solo admiten operandos de tipos idénticos. Cuando los tipos no coinciden, se muestra un error.

* Las cadenas son iguales cuando tienen la misma secuencia de caracteres.
* Los números son iguales cuando tienen el mismo valor
* Los booleanos son iguales si ambos son `true` o ambos son `false`.
* Las variables nulas o indefinidas son iguales entre sí y entre sí.

`${varOne == varTwo}` devuelve `true` si `varOne` y `varTwo` son iguales.

`${varOne != varTwo}` devuelve `true` si `varOne` y `varTwo` no son iguales.

Los operadores relacionales solo admiten operandos que sean números. Se muestra un error para todos los demás tipos.

`${varOne > varTwo}` devuelve `true` si `varOne` es más grande que `varTwo`.

`${varOne < varTwo}` devuelve `true` si `varOne` es más pequeña que `varTwo`.

`${varOne >= varTwo}` devuelve `true` si `varOne` es más grande o igual que `varTwo`.

`${varOne <= varTwo}` devuelve `true` si `varOne` es más pequeña o igual que `varTwo`.

### Paréntesis de agrupamiento {#grouping-parentheses}

El operador de agrupación `()` controla la prioridad de la evaluación en las expresiones.

`${varOne && (varTwo || varThree)}`

## Opciones {#options}

Las opciones de expresión pueden actuar sobre la expresión y modificarla, o servir como parámetros cuando se utilizan junto con instrucciones de bloque.

Todo lo que hay después de `@` es una opción:

```xml
${myVar @ optOne}
```

Las opciones pueden tener un valor, que puede ser una variable o un literal:

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

Las opciones múltiples se separan con comas:

```xml
${myVar @ optOne, optTwo=bar}
```

También son posibles las expresiones paramétricas que solo contienen opciones:

```xml
${@ optOne, optTwo=bar}
```

### Formato de cadena {#string-formatting}

Opción que reemplaza los marcadores de posición enumerados, {*n*}, con la variable correspondiente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

## Manipulación de URL {#url-manipulation}

Hay disponible un nuevo conjunto de manipulaciones de URL.

Consulte los siguientes ejemplos sobre su uso:

Agrega la extensión html a una ruta.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Agrega la extensión html y un selector a una ruta.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Agrega la extensión html y un fragmento (#value) a una ruta.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

El `@extension` funciona en todos los casos, comprobando si se debe añadir la extensión o no.

```xml
${ link @ extension = 'html' }
```

### Formato de número/fecha {#number-date-formatting}

HTL permite aplicar formato nativo a números y fechas, sin necesidad de escribir código personalizado. Esto también admite huso horario y configuración regional.

Los siguientes ejemplos muestran que el formato se especifica primero y el valor que necesita formato:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Para obtener más información sobre el formato que puede utilizar, consulte [Especificación de HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### Internacionalización {#internationalization}

Traduce la cadena al idioma del *origen* actual (consulte más abajo), utilizando el [diccionario](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-translator.html?lang=es) actual. Si no se encuentra ninguna traducción, se utiliza la cadena original.

```xml
${'Page' @ i18n}
```

La opción de sugerencia se puede utilizar para proporcionar un comentario a los traductores, especificando el contexto en el que se utiliza el texto:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

La fuente predeterminada para el idioma es `resource`, lo que significa que el texto se traduce al mismo idioma que el contenido. Esto se puede cambiar a `user`, lo que significa que el idioma se toma de la configuración regional del explorador o de la configuración regional del usuario que ha iniciado sesión:

```xml
${'Page' @ i18n, source='user'}
```

Si se proporciona una configuración regional explícita, se anula la configuración de origen:

```xml
${'Page' @ i18n, locale='en-US'}
```

Para insertar variables en una cadena traducida, se puede utilizar la opción de formato:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Combinación de matrices {#array-join}

De forma predeterminada, cuando se muestra una matriz como texto, HTL muestra valores separados por coma (sin espaciado).

Utilice la opción de unión para especificar un separador diferente:

```xml
${['one', 'two'] @ join='; '}
```

### Mostrar contexto {#display-context}

El contexto de visualización de una expresión HTL hace referencia a su ubicación dentro de la estructura de la página HTML. Por ejemplo, si la expresión aparece en su lugar y produce un nodo de texto una vez procesado, se dice que está en un contexto `text`. Si se encuentra dentro del valor de un atributo, se dice que está en un contexto `attribute`, etc.

Con la excepción de los contextos de script (JS) y estilo (CSS), HTL detectará automáticamente el contexto de las expresiones y las escapará adecuadamente, para evitar problemas de seguridad XSS. En el caso de las secuencias de comandos y CSS, el comportamiento de contexto deseado debe establecerse explícitamente. Además, el comportamiento del contexto también se puede establecer explícitamente en cualquier otro caso en el que se desee una anulación del comportamiento automático.

Aquí tenemos tres variables en tres contextos diferentes:

* `properties.link` (contexto de `uri`)
* `properties.title` (contexto de `attribute`)
* `properties.text` (contexto de `text`)

HTL escapará a cada uno de ellos de forma diferente de acuerdo con los requisitos de seguridad de sus respectivos contextos. No se requiere una configuración de contexto explícita en casos normales como este:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Para generar de forma segura un marcado (es decir, donde la expresión misma se evalúa como HTML), se utiliza el contexto `html`:

```xml
<div>${properties.richText @ context='html'}</div>
```

Se debe establecer un contexto explícito para los contextos de estilo:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Se debe establecer un contexto explícito para los contextos de script:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

La protección de escape y XSS también se puede desactivar:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Configuración de contexto {#context-settings}

| Contexto | Cuándo se usa | Qué hace |
|--- |--- |--- |
| `text` | Valor predeterminado para el contenido dentro de los elementos | Codifica todos los caracteres especiales HTML. |
| `html` | Para generar el marcado de forma segura | Filtra HTML para cumplir las reglas de directiva AntiSamy, lo que elimina lo que no coincide con las reglas. |
| `attribute` | Valor predeterminado para valores de atributo | Codifica todos los caracteres especiales HTML. |
| `uri` | Para mostrar vínculos y rutas Valores predeterminados de los atributos href y src | Valida el URI para escribir como un valor de atributo href o src; no genera nada si la validación falla. |
| `number` | Para mostrar números | Valida el URI para contener un entero, genera cero si la validación falla. |
| `attributeName` | Predeterminado para data-sly-attribute al configurar nombres de atributo | Valida el nombre del atributo y no genera nada si la validación falla. |
| `elementName` | Predeterminado para data-sly-element | Valida el nombre del elemento y no genera nada si la validación falla. |
| `scriptToken` | Para identificadores JS, números literales o cadenas literales | Valida el token de JavaScript y no genera nada si la validación falla. |
| `scriptString` | Dentro de cadenas JS | Codifica los caracteres que saldrían de la cadena. |
| `scriptComment` | Dentro de los comentarios de JS | Valida el comentario de JavaScript y no genera nada si la validación falla. |
| `styleToken` | Para identificadores CSS, números, dimensiones, cadenas, colores hexadecimales o funciones. | Valida el token CSS y no genera nada si la validación falla. |
| `styleString` | Dentro de cadenas CSS | Codifica los caracteres que saldrían de la cadena. |
| `styleComment` | Dentro de comentarios CSS | Valida el comentario CSS y no genera nada si la validación falla. |
| `unsafe` | Solo si ninguno de los anteriores hace el trabajo | Deshabilita completamente la protección XSS y el escape. |
