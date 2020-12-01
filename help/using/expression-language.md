---
title: Lenguaje de expresión HTL
description: El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML.
translation-type: tm+mt
source-git-commit: c7fa6014cd954a2ccb175e4c3a6be9deb83af890
workflow-type: tm+mt
source-wordcount: '1854'
ht-degree: 1%

---


# Lenguaje de expresión HTL {#htl-expression-language}

El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML. Estas expresiones están delimitadas por caracteres `${` y `}`. Para evitar el formato incorrecto de HTML, las expresiones solo se pueden usar en valores de atributos, en contenido de elementos o en comentarios.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Las expresiones se pueden escapar anteponiendo un carácter `\`; por ejemplo, `\${test}` se procesará `${test}`.

>[!NOTE]
>
>Para probar los ejemplos que se proporcionan en esta página, se puede utilizar un entorno de ejecución en directo denominado [Loop de impresión Eval de lectura](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl).

La sintaxis de expresión incluye [variables](#variables), [literales](#literals), [operadores](#operators) y [opciones](#options):

## Variables {#variables}

Las variables son contenedores que almacenan valores u objetos de datos. Los nombres de las variables se denominan identificadores.

Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que normalmente estaban disponibles en JSP después de incluir `global.jsp`. La página [Objetos globales](global-objects.md) proporciona la lista de todos los objetos a los que HTL proporciona acceso.

### Acceso a propiedad {#property-access}

Existen dos formas de acceder a las propiedades de las variables, con notación de puntos o con notación de corchetes:

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

En la mayoría de los casos, se debe preferir la notación de puntos más sencilla y la notación de corchetes debe utilizarse para acceder a propiedades que contengan caracteres de identificador no válidos o para acceder a propiedades dinámicamente. Los dos capítulos siguientes proporcionarán detalles sobre estos dos casos.

Las propiedades a las que se accede pueden ser funciones, aunque no se admiten argumentos de paso, por lo que solo se puede acceder a funciones que no esperan argumentos, como captadores. Se trata de una limitación deseada, que tiene por objeto reducir la cantidad de lógica incrustada en las expresiones. Si es necesario, la sentencia [`data-sly-use`](block-statements.md#use) puede utilizarse para pasar parámetros a la lógica.

También se muestra en el ejemplo anterior que se puede acceder a las funciones de captador de Java, como `getTitle()`, sin anteponer el `get` y reduciendo el caso del carácter que sigue.

### Caracteres de identificador válidos {#valid-identifier-characters}

Los nombres de las variables, denominados identificadores, se ajustan a determinadas reglas. Deben estar en inicio con una letra (`A`-`Z` y `a`-`z`), o un guión bajo (`_`), y los caracteres posteriores también pueden ser dígitos (`0`-`9`) o dos puntos (`:`). Las letras Unicode como `å` y `ü` no se pueden usar en identificadores.

Dado que el carácter de dos puntos (`:`) es común en los nombres de propiedades de AEM, se debe enfatizar que es convenientemente un carácter de identificador válido:

`${properties.jcr:title}`

La notación de corchetes se puede utilizar para acceder a propiedades que contienen caracteres de identificador no válidos, como el carácter de espacio del ejemplo siguiente:

`${properties['my property']}`

### Acceso dinámico a los miembros {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Administración permisiva de valores nulos {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## Literales {#literals}

Un literal es una notación para representar un valor fijo.

### Booleano {#boolean}

Boolean representa una entidad lógica y puede tener dos valores: `true` y `false`.

`${true} ${false}`

### Números {#numbers}

Solo hay un tipo de número: enteros positivos. Mientras que otros formatos numéricos, como punto flotante, son compatibles con las variables, pero no pueden expresarse como literales.

`${42}`

### Cadenas {#strings}

Las cadenas representan datos textuales y pueden estar entre comillas simples o dobles:

`${'foo'} ${"bar"}`

Además de los caracteres ordinarios, se pueden utilizar los siguientes caracteres especiales:

* `\\` Carácter de barra invertida
* `\'` Comilla simple (o apóstrofo)
* `\"` Comilla de doble
* `\t` Ficha
* `\n` Nueva línea
* `\r` Retorno de carro
* `\f` Fuente de formulario
* `\b` Retroceso
* `\uXXXX` Carácter Unicode especificado por los cuatro dígitos hexadecimales XXXX.\
   Algunas secuencias de escape unicode útiles son:

   * `\u0022` para `"`
   * `\u0027` para `'`

Para los caracteres que no aparecen en la lista anterior, si se precede a un carácter de barra invertida se mostrará un error.

A continuación se muestran algunos ejemplos de cómo utilizar el escape de cadenas:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

lo que resultará en la siguiente salida, ya que HTL aplicará un escape específico del contexto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Arreglos {#arrays}

Una matriz es un conjunto ordenado de valores a los que se puede hacer referencia con un nombre y un índice. Los tipos de elementos pueden mezclarse.

```xml
${[1,2,3,4]}
${myArray[2]}
```

Las matrices son útiles para proporcionar una lista de los valores de la plantilla.

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## Operadores {#operators}

### Operadores lógicos {#logical-operators}

Estos operadores se utilizan generalmente con valores booleanos, sin embargo, como en JavaScript, en realidad devuelven el valor de uno de los operandos especificados, por lo que cuando se utilizan con valores no booleanos, pueden devolver un valor no booleano.

Si un valor se puede convertir a `true`, el valor se denomina true. Si un valor se puede convertir a `false`, el valor se denomina falsedad. Los valores que se pueden convertir a `false` son variables no definidas, valores nulos, el número cero y cadenas vacías.

#### NOT lógico {#logical-not}

`${!myVar}` devuelve  `false` si su operando único se puede convertir a  `true`; de lo contrario, devuelve  `true`.

Esto se puede utilizar, por ejemplo, para invertir una condición de prueba, como mostrar un elemento solo si no hay páginas secundarias:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND lógico {#logical-and}

`${varOne && varTwo}` devuelve  `varOne` si es falsedad; de lo contrario, devuelve  `varTwo`.

Este operador puede utilizarse para probar dos condiciones a la vez, como la verificación de la existencia de dos propiedades:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

El operador lógico AND también puede utilizarse para mostrar condicionalmente atributos HTML, ya que HTL elimina atributos con valores configurados dinámicamente que se evalúan como false o en una cadena vacía. Por lo tanto, en el ejemplo siguiente, el atributo `class` sólo se muestra si `logic.showClass` es verdadero y si `logic.className` existe y no está vacío:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR lógico {#logical-or}

`${varOne || varTwo}` regresa  `varOne` si es veraz; de lo contrario, devuelve  `varTwo`.

Este operador puede utilizarse para comprobar si se aplica una de las dos condiciones, como la verificación de la existencia de al menos una propiedad:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Como el operador lógico OR devuelve la primera variable que es verdadera, también puede utilizarse muy convenientemente para proporcionar valores de reserva.

También se puede utilizar para mostrar condicionalmente atributos HTML, ya que HTL elimina atributos con valores establecidos por expresiones que se evalúan como false o como una cadena vacía. Por lo tanto, el ejemplo siguiente mostrará **`properties.jcr:`** title si existe y no está vacío; de lo contrario, volverá a mostrar **`properties.jcr:description`** si existe y no está vacío; de lo contrario, mostrará el mensaje &quot;no se proporciona título ni descripción&quot;:

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operador condicional (ternario) {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` devuelve  `varOne` si  `varCondition` es veraz; de lo contrario, devuelve  `varTwo`.

Normalmente, este operador se puede usar para definir condiciones dentro de expresiones, como mostrar un mensaje diferente en función del estado de la página:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>Dado que los identificadores también admiten caracteres de dos puntos, es mejor separar los operadores ternarios con un espacio en blanco para proporcionar claridad al analizador:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operadores de comparación {#comparison-operators}

Los operadores de igualdad y desigualdad solo admiten operandos de tipos idénticos. Cuando los tipos no coinciden, se muestra un error.

* Las cadenas son iguales cuando tienen la misma secuencia de caracteres.
* Los números son iguales cuando tienen el mismo valor
* Los booleanos son iguales si ambos son `true` o ambos son `false`.
* Las variables nulas o no definidas son iguales entre sí y entre sí.

`${varOne == varTwo}` devuelve  `true` si  `varOne` y  `varTwo` son iguales.

`${varOne != varTwo}` devuelve  `true` si  `varOne` y no  `varTwo` son iguales.

Los operadores relacionales solo admiten operandos que son números. Para todos los demás tipos, se muestra un error.

`${varOne > varTwo}` devuelve  `true` si  `varOne` es bueno que  `varTwo`.

`${varOne < varTwo}` devuelve  `true` si  `varOne` es menor que  `varTwo`.

`${varOne >= varTwo}` devuelve  `true` si  `varOne` es bueno o igual a  `varTwo`.

`${varOne <= varTwo}` devuelve  `true` si  `varOne` es menor o igual a  `varTwo`.

### Paréntesis de agrupación {#grouping-parentheses}

El operador de agrupación `()` controla la prioridad de la evaluación en expresiones.

`${varOne && (varTwo || varThree)}`

## Opciones {#options}

Las opciones de expresión pueden actuar en la expresión y modificarla, o servir como parámetros cuando se utilizan junto con sentencias block.

Todo después de `@` es una opción:

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

Las opciones múltiples están separadas por comas:

```xml
${myVar @ optOne, optTwo=bar}
```

También son posibles las expresiones paramétricas que contienen solo opciones:

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

Añade la extensión html en una ruta.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Añade la extensión html y un selector en una ruta.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Añade la extensión html y un fragmento (#value) en una ruta.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

El `@extension` funciona en todos los escenarios, comprobando si se debe agregar la extensión o no.

```xml
${ link @ extension = 'html' }
```

### Formato de número/fecha {#number-date-formatting}

HTL permite el formato nativo de números y fechas, sin necesidad de escribir código personalizado. Esto también admite huso horario y configuración regional.

Los siguientes ejemplos muestran que el formato se especifica primero y, a continuación, el valor que necesita formato:

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>Para obtener información detallada sobre el formato que puede utilizar, consulte [HTL-Specification](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### Internacionalización {#internationalization}

Traduce la cadena al idioma del *origen* actual (véase más adelante), utilizando el [diccionario](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/internationalization/i18n-translator.html) actual. Si no se encuentra ninguna traducción, se utiliza la cadena original.

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

Para incrustar variables en una cadena traducida, se puede utilizar la opción de formato:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Unión de arreglos de discos {#array-join}

De forma predeterminada, al mostrar una matriz como texto, HTL mostrará valores separados por comas (sin espaciado).

Utilice la opción de unión para especificar un separador diferente:

```xml
${['one', 'two'] @ join='; '}
```

### Mostrar contexto {#display-context}

El contexto de visualización de una expresión HTL hace referencia a su ubicación dentro de la estructura de la página HTML. Por ejemplo, si la expresión aparece en su lugar y produce un nodo de texto una vez procesado, se dice que se encuentra en un contexto `text`. Si se encuentra dentro del valor de un atributo, se dice que se encuentra en un contexto `attribute`, y así sucesivamente.

Con excepción de los contextos de secuencias de comandos (JS) y estilos (CSS), HTL detectará automáticamente el contexto de las expresiones y las eliminará de forma adecuada para evitar problemas de seguridad XSS. En el caso de secuencias de comandos y CSS, el comportamiento de contexto deseado debe establecerse explícitamente. Además, el comportamiento de contexto también se puede establecer explícitamente en cualquier otro caso en el que se desee anular el comportamiento automático.

Aquí tenemos tres variables en tres contextos diferentes:

* `properties.link` (  `uri` contexto)
* `properties.title` (`attribute` contexto)
* `properties.text` (`text` contexto)

HTL se librará de cada uno de ellos de manera diferente de acuerdo con los requisitos de seguridad de sus respectivos contextos. No se requiere una configuración de contexto explícita en casos normales como este:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Para generar de forma segura el marcado (es decir, donde la propia expresión se evalúa como HTML), se utiliza el contexto `html`:

```xml
<div>${properties.richText @ context='html'}</div>
```

El contexto explícito debe establecerse para los contextos de estilo:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

Se debe establecer un contexto explícito para los contextos de secuencias de comandos:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

La protección de escape y XSS también se puede desactivar:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Configuración de contexto {#context-settings}

| Contexto | Cuándo usar | Qué hace |
|--- |--- |--- |
| `text` | Predeterminado para el contenido dentro de los elementos | Codifica todos los caracteres especiales HTML. |
| `html` | Para generar marcas de forma segura | Filtros HTML para cumplir las reglas de directiva AntiSamy, eliminando lo que no coincide con las reglas. |
| `attribute` | Valor predeterminado para valores de atributo | Codifica todos los caracteres especiales HTML. |
| `uri` | Visualización de vínculos y rutas Predeterminado para los valores de atributo href y src | Valida el URI para escribir como un valor de atributo href o src; no genera nada si falla la validación. |
| `number` | Para mostrar números | Valida el URI para contener un entero, devuelve cero si se produce un error en la validación. |
| `attributeName` | Predeterminado para data-astode al establecer nombres de atributos | Valida el nombre del atributo y no genera nada si falla la validación. |
| `elementName` | Predeterminado para data-hidden-element | Valida el nombre del elemento y no genera nada si falla la validación. |
| `scriptToken` | Para identificadores JS, números literales o cadenas literales | Valida el token de JavaScript y no genera nada si falla la validación. |
| `scriptString` | Dentro de cadenas JS | Codifica los caracteres que saldrían de la cadena. |
| `scriptComment` | Dentro de los comentarios de JS | Valida el comentario de JavaScript y no genera nada si falla la validación. |
| `styleToken` | Para identificadores CSS, números, dimensiones, cadenas, colores hexadecimales o funciones. | Valida el token de CSS y no genera nada si falla la validación. |
| `styleString` | Dentro de cadenas CSS | Codifica los caracteres que saldrían de la cadena. |
| `styleComment` | En comentarios CSS | Valida el comentario CSS y no genera nada si falla la validación. |
| `unsafe` | Sólo si ninguno de los anteriores hace el trabajo | Deshabilita completamente el escape y la protección XSS. |
