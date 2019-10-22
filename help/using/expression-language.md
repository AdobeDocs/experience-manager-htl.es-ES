---
title: Lenguaje de expresión HTL
seo-title: Lenguaje de expresión HTL
description: El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML.
seo-description: 'El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML. '
uuid: 38b4a259-03b5-4847-91c6-e20377600070
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 9ba37ca0-f318-48b0-a791-a944a72502ed
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 6de5ed20e4463c0c2e804e24cb853336229a7c1f

---


# Lenguaje de expresión HTL {#htl-expression-language}

El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML. Estas expresiones están delimitadas por caracteres `${` y `}`. Para evitar el formato incorrecto de HTML, las expresiones solo se pueden usar en valores de atributo, en contenido de elemento o en comentarios.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Las expresiones se pueden escapar con un **`\`** carácter, por ejemplo, **`\${test}`** se procesarán **`${test}`**.

>[!NOTE]
>
>Para probar los ejemplos que se proporcionan en esta página, se puede utilizar un entorno de ejecución en directo denominado [Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) de impresión Eval de lectura.

La sintaxis de expresión incluye [variables](#variables), [literales](#literals), [operadores](#operators) y [opciones](#options):

## Variables {#variables}

Las variables son contenedores que almacenan valores u objetos de datos. Los nombres de las variables se denominan identificadores.

Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que normalmente estaban disponibles en JSP después de incluirlos `global.jsp`. La página Objetos [](global-objects.md) globales proporciona la lista de todos los objetos a los que HTL proporciona acceso.

### Acceso a propiedades {#property-access}

Existen dos formas de acceder a las propiedades de variables, con notación de puntos o con notación de corchetes:

```
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

En la mayoría de los casos, se debe preferir la notación de puntos más sencilla y la notación de corchetes debe utilizarse para acceder a propiedades que contengan caracteres de identificador no válidos o para acceder a propiedades dinámicamente. Los dos capítulos siguientes proporcionarán detalles sobre estos dos casos.

Las propiedades a las que se accede pueden ser funciones, aunque no se admiten argumentos de paso, por lo que solo se puede acceder a funciones que no esperan argumentos, como captadores. Se trata de una limitación deseada, que tiene por objeto reducir la cantidad de lógica incrustada en las expresiones. Si es necesario, la [`data-sly-use`](block-statements.md#use) sentencia se puede utilizar para pasar parámetros a la lógica.

También se muestra en el ejemplo anterior que se puede acceder a las funciones de captador de Java, como `getTitle()`, sin anteponer el carácter **`get`**, y reduciendo el caso del carácter que sigue.

### Caracteres de identificación válidos {#valid-indentifier-characters}

Los nombres de las variables, denominados identificadores, se ajustan a determinadas reglas. Deben comenzar con una letra (**`A`**-**`Z`** y **`a`**-**`z`**) o un guión bajo (**`_`**), y los caracteres posteriores también pueden ser dígitos (**`0`**-**`9`**) o dos puntos (**`:`**). Las letras Unicode como **`å`** y **`ü`** no se pueden usar en identificadores.

Dado que el carácter de dos puntos (**:**) es común en los nombres de propiedades de AEM, es conveniente que sea un carácter de identificador válido:

`${properties.jcr:title}`

La notación de corchetes se puede utilizar para acceder a propiedades que contienen caracteres de identificador no válidos, como el carácter de espacio del ejemplo siguiente:

`${properties['my property']}`

### Acceso dinámico a los miembros {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Gestión permisiva de valores nulos {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## Literales {#literals}

Un literal es una anotación para representar un valor fijo.

### Booleano {#boolean}

Boolean representa una entidad lógica y puede tener dos valores: **`true`**, y **`false`**.

`${true} ${false}`

### Números {#numbers}

Solo hay un tipo de número: enteros positivos. Mientras que otros formatos numéricos, como punto flotante, son compatibles con las variables, pero no pueden expresarse como literales.

`${42}`

### Cadenas {#strings}

Representan datos de texto y pueden estar entre comillas simples o dobles:

`${'foo'} ${"bar"}`

Además de los caracteres ordinarios, se pueden utilizar los siguientes caracteres especiales:

* **`\\`** Carácter de barra invertida
* **`\'`** Comilla simple (o apóstrofo)
* **`\"`** Comilla doble
* **`\t`** Tabulación
* **`\n`** Nueva línea
* **`\r`** Retorno de carro
* **`\f`** Fuente de formulario
* **`\b`** Retroceso
* `\uXXXX` Carácter Unicode especificado por los cuatro dígitos hexadecimales XXXX.\
   Algunas secuencias de escape unicode útiles son:

   * **\u0022** para **"**
   * **\u0027** para **'**

En el caso de caracteres no enumerados arriba, si se precede a un carácter de barra invertida se mostrará un error.

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

### Matrices {#arrays}

Una matriz es un conjunto ordenado de valores a los que se puede hacer referencia con un nombre y un índice. Los tipos de elementos pueden mezclarse.

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

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

Estos operadores se utilizan generalmente con valores booleanos, sin embargo, como en JavaScript, en realidad devuelven el valor de uno de los operandos especificados, por lo que cuando se utilizan con valores no booleanos, pueden devolver un valor no booleano.

Si se puede convertir un valor a **`true`**, el valor se denomina true. Si se puede convertir un valor a **`false`**, este se denomina falsedad. Los valores a los que se puede convertir **`false`** son: variables no definidas, valores nulos, el número cero y cadenas vacías.

#### NOT lógico {#logical-not}

**`${!myVar}`** devuelve **`false`** si su operando único se puede convertir a `true`; en caso contrario, devuelve **`true`**.

Esto se puede utilizar, por ejemplo, para invertir una condición de prueba, como mostrar un elemento solo si no hay páginas secundarias:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND lógico {#logical-and}

**`${varOne && varTwo}`** devuelve `varOne` si es falsedad; en caso contrario, devuelve **varTwo**.

Este operador puede utilizarse para probar dos condiciones a la vez, como la verificación de la existencia de dos propiedades:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

El operador lógico AND también puede utilizarse para mostrar condicionalmente atributos HTML, ya que HTL elimina atributos con valores configurados dinámicamente que se evalúan como false o en una cadena vacía. Por lo tanto, en el ejemplo siguiente, el **`class`** atributo solo se muestra si **`logic.showClass`** es verdadero y si **`logic.className`** existe y no está vacío:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR lógico {#logical-or}

**`${varOne || varTwo}`** devuelve **varOne** si es cierto; en caso contrario, devuelve **varTwo**.

Este operador puede utilizarse para comprobar si se aplica una de las dos condiciones, como la verificación de la existencia de al menos una propiedad:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Como el operador lógico OR devuelve la primera variable que es verdadera, también puede utilizarse muy convenientemente para proporcionar valores de reserva.

visualización condicional de atributos HTML, ya que HTL elimina atributos con valores establecidos por expresiones que se evalúan como false o como una cadena vacía. Así que el ejemplo de abajo mostrará **`properties.jcr:`** el título si existe y no está vacío, de lo contrario volverá a mostrarse **`properties.jcr:description`** si existe y no está vacío. De lo contrario, mostrará el mensaje "no se proporciona título ni descripción":

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operador condicional (ternario) {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** devuelve **`varOne`** si **`varCondition`** es veraz; de lo contrario, devuelve **`varTwo`**.

Este operador se puede utilizar normalmente para definir condiciones dentro de las expresiones, como mostrar un mensaje diferente en función del estado de la página:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

Una nota importante es que, dado que los identificadores también admiten caracteres de dos puntos, es mejor separar los operadores ternarios con un espacio en blanco para proporcionar claridad al analizador:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operadores de comparación {#comparison-operators}

Los operadores de igualdad y desigualdad solo admiten operandos de tipos idénticos. Cuando los tipos no coinciden, se muestra un error.

* Las cadenas son iguales cuando tienen la misma secuencia de caracteres.
* Los números son iguales cuando tienen el mismo valor
* Los booleanos son iguales si ambos lo son **`true`** o ambos lo son **`false`**.

* Las variables nulas o no definidas son iguales entre sí y entre sí.

**`${varOne == varTwo}`** devuelve **`true`** si **`varOne`** y **`varTwo`** son iguales.

**`${varOne != varTwo}`** devuelve **`true`** si **`varOne`** y **`varTwo`** no son iguales.

Los operadores relacionales solo admiten operandos que son números. Para todos los demás tipos, se muestra un error.

**`${varOne > varTwo}`** devuelve **`true`** si **`varOne`** es mayor que **`varTwo`**.

**`${varOne < varTwo}`** devuelve **`true`** si **`varOne`** es menor que **`varTwo`**.

**`${varOne >= varTwo}`** devuelve **`true`** si **`varOne`** es mayor o igual a **`varTwo`**.

**`${varOne <= varTwo}`** devuelve **`true`** si **`varOne`** es menor o igual a **`varTwo`**.

### Paréntesis de agrupación {#grouping-parentheses}

El operador de agrupación **`(`****`)`** controla la prioridad de la evaluación en las expresiones.

`${varOne && (varTwo || varThree)}`

## Opciones {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

Las opciones de expresión pueden actuar en la expresión y modificarla, o servir como parámetros cuando se utilizan junto con sentencias block.

Todo después de **`@`** esto es una opción:

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

### Internacionalización {#internationalization}

Traduce la cadena al idioma del *origen* actual (véase más abajo), mediante el [diccionario](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator)actual. Si no se encuentra ninguna traducción, se utiliza la cadena original.

```xml
${'Page' @ i18n}
```

La opción de sugerencia se puede utilizar para proporcionar un comentario a los traductores, especificando el contexto en el que se utiliza el texto:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

La fuente predeterminada para el idioma es 'resource', lo que significa que el texto se traduce al mismo idioma que el contenido. Esto se puede cambiar a 'user', lo que significa que el idioma se toma de la configuración regional del explorador o de la configuración regional del usuario que ha iniciado sesión:

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

### Unión de arreglos {#array-join}

De forma predeterminada, al mostrar una matriz como texto, HTL mostrará valores separados por comas (sin espaciado).

Utilice la opción de unión para especificar un separador diferente:

```xml
${['one', 'two'] @ join='; '}
```

### Mostrar contexto {#display-context}

El contexto de visualización de una expresión HTL hace referencia a su ubicación dentro de la estructura de la página HTML. Por ejemplo, si la expresión aparece en su lugar y produce un nodo de texto una vez procesado, se dice que se encuentra en un **`text`** contexto. Si se encuentra dentro del valor de un atributo, se dice que se encuentra en un **`attribute`** contexto, etc.

A excepción de los contextos de secuencias de comandos (JS) y estilos (CSS), HTL detectará automáticamente el contexto de las expresiones y las eliminará de forma adecuada para evitar problemas de seguridad XSS. En el caso de secuencias de comandos y CSS, el comportamiento de contexto deseado debe establecerse explícitamente. Además, el comportamiento de contexto también se puede establecer explícitamente en cualquier otro caso en el que se desee anular el comportamiento automático.

Aquí tenemos tres variables en tres contextos diferentes: **`properties.link`** ( `uri` contexto), **`properties.title`** (**`attribute`** contexto) y **`properties.text`**(**`text`** contexto). HTL se librará de cada uno de ellos de manera diferente según los requisitos de seguridad de sus respectivos contextos. No se requiere una configuración de contexto explícita en casos normales como este:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Para generar de forma segura el marcado (es decir, donde la expresión se evalúa como HTML), se utiliza el `html` contexto:

```xml
<div>${properties.richText @ context='html'}</div>
```

El contexto explícito debe configurarse para contextos de estilo:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

El contexto explícito debe configurarse para contextos de secuencia de comandos:

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
| text | Predeterminado para el contenido dentro de los elementos | Codifica todos los caracteres especiales HTML. |
| html | Para generar marcas de forma segura | Filtra HTML para cumplir las reglas de directiva AntiSamy, eliminando lo que no coincide con las reglas. |
| atributo | Valor predeterminado para valores de atributo | Codifica todos los caracteres especiales HTML. |
| uri | Visualización de vínculos y rutas Predeterminado para los valores de atributo href y src | Valida el URI para escribir como un valor de atributo href o src; no genera nada si falla la validación. |
| número | Para mostrar números | Valida el URI para contener un entero, devuelve cero si se produce un error en la validación. |
| attributeName | Predeterminado para data-astode al establecer nombres de atributos | Valida el nombre del atributo y no genera nada si falla la validación. |
| elementName | Predeterminado para data-hidden-element | Valida el nombre del elemento y no genera nada si falla la validación. |
| scriptToken | Para identificadores JS, números literales o cadenas literales | Valida el token de JavaScript y no genera nada si falla la validación. |
| scriptString | Dentro de cadenas JS | Codifica los caracteres que saldrían de la cadena. |
| scriptComment | Dentro de los comentarios de JS | Valida el comentario de JavaScript y no genera nada si falla la validación. |
| styleToken | Para identificadores CSS, números, dimensiones, cadenas, colores hexadecimales o funciones. | Valida el token de CSS y no genera nada si falla la validación. |
| styleString | Dentro de cadenas CSS | Codifica los caracteres que saldrían de la cadena. |
| styleComment | En comentarios CSS | Valida el comentario CSS y no genera nada si falla la validación. |
| inseguro | Sólo si ninguno de los anteriores hace el trabajo | Deshabilita completamente el escape y la protección XSS. |

