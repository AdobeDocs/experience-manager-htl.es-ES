---
title: Lenguaje de expresión HTL
seo-title: Lenguaje de expresión HTL
description: El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder
  a las estructuras de datos que proporcionan los elementos dinámicos de la salida
  HTML.
seo-description: El lenguaje de plantilla HTML utiliza un lenguaje de expresión para
  acceder a las estructuras de datos que proporcionan los elementos dinámicos de la
  salida HTML.
uuid: 38 b 4 a 259-03 b 5-4847-91 c 6-e 20377600070
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 9 ba 37 ca 0-f 318-48 b 0-a 791-a 944 a 72502 ed
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# Lenguaje de expresión HTL {#htl-expression-language}

El lenguaje de plantilla HTML utiliza un lenguaje de expresión para acceder a las estructuras de datos que proporcionan los elementos dinámicos de la salida HTML. Estas expresiones están delimitadas por caracteres `${` y `}`. Para evitar HTML incorrecto, las expresiones solo se pueden utilizar en valores de atributo, en contenido de elementos o en comentarios.

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

Expressions can be escaped by prepended by a **`\`** character, for instance **`\${test}`** will render **`${test}`**.

>[!NOTE]
>
>Para probar los ejemplos proporcionados en esta página, se puede utilizar un entorno de ejecución activo denominado [Leer bucle](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) de impresión eval.

La sintaxis de expresión incluye [variables](#variables), [literales](#literals), [operadores](#operators) y [opciones](#options):

## Variables {#variables}

Las variables son contenedores que almacenan valores u objetos de datos. Los nombres de las variables se denominan identificadores.

Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que estaban comúnmente disponibles en JSP después de incluir `global.jsp`. La página [Objetos](global-objects.md) globales proporciona la lista de todos los objetos proporcionados por HTL.

### Acceso a propiedades {#property-access}

Existen dos maneras de acceder a propiedades de variables, con notación de punto o con notación de corchetes:

`${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}`

La notación de punto simple debe ser preferida para la mayoría de los casos, y la notación entre corchetes debe utilizarse para acceder a propiedades que contengan caracteres de identificador no válidos o para acceder de forma dinámica a propiedades. Los dos capítulos siguientes proporcionan detalles sobre estos dos casos.

Las propiedades de acceso pueden ser funciones, pero no se admiten argumentos, por lo que solo las funciones que no esperan argumentos pueden acceder a ellas, como captadores. Ésta es una limitación que se desea, con el objetivo de reducir la cantidad de lógica incrustada en expresiones. Si es necesario, la [`data-sly-use`](block-statements.md#use) sentencia se puede utilizar para pasar parámetros a la lógica.

También se muestra en el ejemplo anterior que se puede acceder a las funciones getter Java, como `getTitle()`puede ser, sin anteponer la **`get`**operación y al reducir el caso del siguiente carácter.

### Caracteres de Indentifier válidos {#valid-indentifier-characters}

Los nombres de las variables, denominados identificadores, cumplen ciertas reglas. Deben comenzar con una letra (**`A`**-**`Z`** y **`a`**-)**`z`**, o con subrayado (**`_`**), y los caracteres posteriores también pueden ser dígitos (**`0`**-**`9`**) o dos puntos (**`:`**). Letras Unicode como, por ejemplo **`å`** , **`ü`** no se pueden utilizar en identificadores.

Dado que el carácter dos puntos (**:**) es común en los nombres de propiedad de AEM, es conveniente que sea un carácter de identificador válido:

`${properties.jcr:title}`

La notación de corchetes puede utilizarse para acceder a propiedades que contienen caracteres de identificador no válidos, como el carácter de espacio del ejemplo siguiente:

`${properties['my property']}`

### Acceso dinámico a miembros {#accessing-members-dynamically}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${properties[myVar]}
```

### Manejo permisivo de valores nulos {#permissive-handling-of-null-values}

<!-- 

Comment Type: draft

<p>TODO: add description</p>

 -->

```xml
${currentPage.lastModified.time.toString}
```

## Literales {#literals}

Un literal es una notación para representar un valor fijo.

### Booleano {#boolean}

El booleano representa una entidad lógica y puede tener dos valores: **`true`**y **`false`**.

`${true} ${false}`

### Números {#numbers}

Solo hay un tipo de número: enteros positivos. Mientras que otros formatos de números, como punto flotante, son admitidos en variables, pero no se pueden expresar como literales.

`${42}`

### Cadenas {#strings}

Representan datos de texto y pueden ser simples o dobles:

`${'foo'} ${"bar"}`

Además de los caracteres normales, se pueden utilizar caracteres especiales:

* **`\\`** Carácter de barra invertida
* **`\'`** Comilla simple (o apóstrofo)
* **`\"`** Comillas dobles
* **`\t`** Tabulación
* **`\n`** Nueva línea
* **`\r`** Retorno de carro
* **`\f`** Fuente de formulario
* **`\b`** Retroceso
* `\uXXXX` El carácter Unicode especificado por los cuatro dígitos hexadecimales XXXX.\
   Algunas secuencias de escape unicode útiles son:

   * **\ u 0022** for **"**
   * **\ u 0027** for **'**

Para los caracteres no enumerados anteriormente, al precargar un caracter de barra invertida se mostrará un error.

A continuación se muestran algunos ejemplos de cómo utilizar la cadena escape:

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

que dará como resultado una salida siguiente, ya que HTL aplicará escapamiento específico del contexto:

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### Matrices {#arrays}

Una matriz es un conjunto ordenado de valores a los que se puede hacer referencia con un nombre y un índice. Los tipos de sus elementos pueden mezclarse.

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

Estos operadores suelen utilizarse con valores booleanos. Sin embargo, como en JavaScript, en realidad devuelven el valor de uno de los operandos especificados, por lo que cuando se utilizan con valores no booleanos, pueden devolver un valor no booleano.

Si se puede convertir un valor, **`true`**el valor se denomina truas. Si se puede convertir un valor, **`false`**el valor se denomina falsy. Los valores a los que se puede convertir **`false`** son: variables no definidas, valores nulos, número cero y cadenas vacías.

#### NOT lógico {#logical-not}

**`${!myVar}`** devuelve **`false`** si se puede convertir un solo operando `true`; de lo contrario, devuelve **`true`**.

Esto puede utilizarse para invertir una condición de prueba, como mostrar un elemento solo si no hay páginas secundarias:

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### AND lógico {#logical-and}

**`${varOne && varTwo}`** devuelve `varOne` si es falsa; De lo contrario, devuelve **vartwo**.

Este operador puede utilizarse para comprobar dos condiciones a la vez, como verificar la existencia de dos propiedades:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

El operador AND lógico también puede utilizarse para mostrar condicionalmente atributos HTML, ya que HTL elimina los atributos con valores configurados dinámicamente como falsos o a una cadena vacía. Así, en el ejemplo siguiente, el **`class`** atributo solo se muestra si **`logic.showClass`** es trueno y si **`logic.className`** existe y no está vacío:

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### OR lógico {#logical-or}

**`${varOne || varTwo}`** devuelve **varone** si es trueno; De lo contrario, devuelve **vartwo**.

Este operador se puede utilizar para comprobar si se aplican dos condiciones, como verificar la existencia de al menos una propiedad:

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

Como el operador lógico OR devuelve la primera variable que es truena, también se puede utilizar muy fácilmente para proporcionar valores de reserva.

muestra condicionalmente atributos HTML, ya que HTL elimina los atributos con valores establecidos por expresiones que evalúan en false o en una cadena vacía. Así que el ejemplo siguiente mostrará****`properties.jcr:`title si existe y no está vacío. De lo contrario, volverá a la dise? si **`properties.jcr:description`** existe y no está vacío. Si no se encuentra, se mostrará el mensaje "sin título o descripción proporcionado":

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### Operador condicional (ternario) {#conditional-ternary-operator}

**`${varCondition ? varOne : varTwo}`** devuelve **`varOne`** si **`varCondition`** es trueno; de lo contrario, devuelve **`varTwo`**.

Este operador suele utilizarse para definir condiciones dentro de expresiones, como mostrar un mensaje diferente basado en el estado de la página:

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

Nota importante, ya que los caracteres de dos puntos también están permitidos en identificadores, es mejor separar los operadores ternarios con un espacio en blanco para proporcionar claridad al analizador:

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### Operadores de comparación {#comparison-operators}

Los operadores de igualdad y desigualdad solo admiten operandos de tipos idénticos. Cuando los tipos no coinciden, se muestra un error.

* Las cadenas son iguales cuando tienen la misma secuencia de caracteres.
* Los números son iguales cuando tienen el mismo valor
* Los booleanos son iguales si ambos son **`true`****`false`**o ambos.

* Las variables nulas o no definidas son iguales a sí mismos y a las demás.

**`${varOne == varTwo}`** devuelve **`true`** si **`varOne`** y **`varTwo`** son iguales.

**`${varOne != varTwo}`** devuelve **`true`** si **`varOne`** y **`varTwo`** no son iguales.

Los operadores relacionales sólo admiten operandos que son números. Para todos los demás tipos, se muestra un error.

**`${varOne > varTwo}`** devuelve **`true`** si **`varOne`** es mayor **`varTwo`**que.

**`${varOne < varTwo}`** devuelve **`true`** si **`varOne`** es menor **`varTwo`**que.

**`${varOne >= varTwo}`** devuelve **`true`** si **`varOne`** es mayor o igual **`varTwo`**a.

**`${varOne <= varTwo}`** devuelve **`true`** si **`varOne`** es menor o igual **`varTwo`**a.

### Paréntesis de agrupación {#grouping-parentheses}

El operador de agrupación **`(`****`)`** controla la prioridad de la evaluación en las expresiones.

`${varOne && (varTwo || varThree)}`

## Opciones {#options}

<!-- 

Comment Type: draft

<p>TODO: review text below.</p>

 -->

Las opciones de expresión pueden actuar en función de la expresión y modificarla, o servir como parámetros cuando se utilizan junto con declaraciones de bloques.

Todo después de la **`@`** opción es una opción:

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

También son posibles las expresiones paramétricas que contienen opciones:

```xml
${@ optOne, optTwo=bar}
```

### Formato de cadena {#string-formatting}

Opción que sustituye a los marcadores de posición enumerados, {*n*}, con la variable correspondiente:

```xml
${'Page {0} of {1}' @ format=[current, total]}
```

### Internacionalización {#internationalization}

Traduce la cadena al idioma de *la fuente actual* (véase más abajo), utilizando el [diccionario actual](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/i18n-translator). Si no se encuentra ninguna traducción, se utiliza la cadena original.

```xml
${'Page' @ i18n}
```

La opción de sugerencia se puede utilizar para proporcionar un comentario para los traductores, especificando el contexto en el que se utiliza el texto:

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

La fuente predeterminada para el idioma es'resource ', lo que significa que el texto se traduce al mismo idioma que el contenido. Esto se puede cambiar a'user ', lo que significa que el idioma se toma de la configuración regional del navegador o de la configuración regional del usuario registrado:

```xml
${'Page' @ i18n, source='user'}
```

Al proporcionar una configuración regional explícita se anula la configuración de origen:

```xml
${'Page' @ i18n, locale='en-US'}
```

Para incrustar variables en una cadena traducida, se puede utilizar la opción de formato:

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### Unión de matriz {#array-join}

De forma predeterminada, al mostrar una matriz como texto, HTL mostrará valores separados por comas (sin espaciado).

Utilice la opción de unión para especificar otro separador:

```xml
${['one', 'two'] @ join='; '}
```

### Mostrar contexto {#display-context}

El contexto de visualización de una expresión HTL hace referencia a su ubicación dentro de la estructura de la página HTML. Por ejemplo, si la expresión aparece en lugar que produciría un nodo de texto una vez procesado, se dice que está en **`text`** un contexto. Si se encuentra dentro del valor de un atributo, se dice que está en **`attribute`** un contexto, etc.

A excepción de los contextos de script (JS) y estilo (CSS), HTL detectará automáticamente el contexto de expresiones y las escapará de forma adecuada, para evitar problemas de seguridad XSS. En el caso de las secuencias de comandos y CSS, se debe definir explícitamente el comportamiento de contexto deseado. Además, el comportamiento de contexto también se puede establecer explícitamente en cualquier otro caso donde se desee anular el comportamiento automático.

Aquí tenemos tres variables en tres contextos diferentes: **`properties.link`** ( `uri` contexto), **`properties.title`** (**`attribute`** contexto) y **`properties.text`**(**`text`** contexto). HTL elimina cada uno de estos valores de acuerdo con los requisitos de seguridad de sus contextos respectivos. No se requiere ninguna configuración de contexto explícita en casos normales como este:

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

Para generar marcado de forma segura (es decir, donde la expresión misma se evalúa como HTML), se utiliza `html` el contexto:

```xml
<div>${properties.richText @ context='html'}</div>
```

El contexto explícito debe configurarse para contextos de estilo:

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

El contexto explícito debe configurarse para los contextos de secuencias de comandos:

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

La protección de XSS y los escapes también se pueden desactivar:

```xml
<div>${myScript @ context='unsafe'}</div>
```

### Configuración de contexto {#context-settings}

| Contexto | Cuándo utilizar | Qué hace |
|--- |--- |--- |
| text | Predeterminado para contenido dentro de elementos | Codifica todos los caracteres especiales HTML. |
| html | Para generar marcado de forma segura | Filtros HTML para cumplir las reglas de política antisamy, eliminando lo que no coincide con las reglas. |
| attribute | Valor predeterminado para valores de atributo | Codifica todos los caracteres especiales HTML. |
| uri | Para mostrar vínculos y rutas predeterminado para valores de atributo href y src | Valida el URI para escribirlo como valor de atributo href o src, no produce ningún problema si falla la validación. |
| número | Para mostrar números | Valida el URI para que contenga un número entero, produce cero si falla la validación. |
| Attributename | Predeterminado para el atributo de datos al configurar nombres de atributos | Valida el nombre de atributo, no genera nada si falla la validación. |
| Elementname | Predeterminado para el elemento de datos oscuro | Valida el nombre del elemento, no genera nada si falla la validación. |
| Scripttoken | Para identificadores JS, números literal o cadenas literales | Valida el autentificador JavaScript, no genera nada si falla la validación. |
| Scriptstring | Dentro de cadenas JS | Codifica los caracteres que podrían saltar de la cadena. |
| Scriptcomment | Dentro de comentarios JS | Valida el comentario JavaScript, no genera nada si falla la validación. |
| Styletoken | Para identificadores CSS, números, dimensiones, cadenas, coloreados hexadecimales o funciones. | Valida el token CSS, no genera nada si falla la validación. |
| Stylestring | Dentro de cadenas CSS | Codifica los caracteres que podrían saltar de la cadena. |
| Stylecomment | Dentro de los comentarios CSS | Valida el comentario CSS, no genera nada si falla la validación. |
| no seguro | Solo si ninguno de los anteriores realiza el trabajo | Desactiva el escape y la protección XSS por completo. |

