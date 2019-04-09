---
title: Introducción a HTL
seo-title: Introducción a HTL
description: HTL admitido por AEM toma el lugar de JSP como el sistema de plantillas
  de lado del servidor preferido y recomendado para HTML en AEM.
seo-description: El lenguaje de plantilla HTML - HTL, compatible con Adobe Experience
  Manager, toma el lugar de JSP como el sistema de plantilla de lado de servidor preferido
  y recomendado para HTML en AEM.
uuid: 4 a 7 d 6748-8 cdf -4280-a 85 d -6 c 5319 abf 487
content-type: referencia
topic-tags: introducción
discoiquuid: 3 bf 2 ca 75-0 d 68-489 d-bd 1 c -1 d 4 fd 730 c 61 a
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 1e3df6159b48da27460f3ad95c22c13d025b1a72

---


# Introducción a HTL {#getting-started-with-htl}

El lenguaje de plantilla HTML (HTL) admitido por Adobe Experience Manager (AEM) toma el lugar de JSP (javaserver Pages) como el sistema de plantilla de lado de servidor preferido y recomendado para HTML en AEM.

>[!NOTE]
>
>Para ejecutar la mayoría de los ejemplos proporcionados en esta página, se puede utilizar un entorno de ejecución activo denominado [Leer bucle](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) de impresión eval.
>
>La comunidad de AEM generó una serie [de artículos, vídeos y seminarios web](related-community-articles.md) relacionados con el uso de HTL.

## HTL sobre JSP {#htl-over-jsp}

Se recomienda utilizar nuevos proyectos de AEM para utilizar el lenguaje de plantilla HTML, ya que ofrece varios beneficios en comparación con JSP. Sin embargo, para los proyectos existentes, una migración solo tiene sentido si se estima que es menor que mantener los JSP existentes en los próximos años.

Pero el paso a HTL no es necesariamente una opción de todo o nada, ya que los componentes escritos en HTL son compatibles con los componentes escritos en JSP o ESP. Es decir, los proyectos existentes pueden sin problemas utilizar HTL para nuevos componentes, al mismo tiempo que mantienen JSP para componentes existentes.

Incluso dentro del mismo componente, los archivos HTL se pueden utilizar junto con JSPS y ESP. En la **línea 1 se muestra** cómo incluir un archivo HTL desde un archivo JSP y, en **la línea 2** , cómo se puede incluir un archivo JSP desde un archivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Preguntas frecuentes{#frequently-asked-questions}

Antes de comenzar con el lenguaje de plantilla HTML, empecemos por responder a algunas preguntas relacionadas con el tema JSP frente a HTL.

**¿Tiene HTL limitaciones que JSP no tiene?**HTL no tiene en realidad limitaciones comparadas con JSP en el sentido de que lo que se puede hacer con JSP también debería alcanzarse con HTL. Sin embargo, HTL es por diseño más estricto que JSP en varios aspectos, y lo que se puede conseguir en un solo archivo JSP puede ser separado en una clase Java o en un archivo JavaScript que se pueda alcanzar en HTL. Pero esto generalmente se desea para garantizar una buena separación de las preocupaciones entre la lógica y la marca.

**¿HTL admite bibliotecas JSP de etiquetas JSP?**No, pero como se muestra en la sección [Cargar bibliotecas](getting-started.md#loading-client-libraries) de cliente, las [declaraciones de plantilla y de llamada](block-statements.md#template-call) ofrecen un patrón similar.

**¿Las funciones HTL se pueden ampliar en un proyecto de AEM?**** No, pero como se muestra en la sección [Cargar bibliotecas](getting-started.md#loading-client-libraries) de cliente, las [declaraciones de plantilla y de llamada](block-statements.md#template-call) ofrecen un patrón similar.
No, no. HTL dispone de potentes mecanismos de extensión para reutilizar la lógica ( [la API de uso](getting-started.md#use-api-for-accessing-logic) [- y del marcado](block-statements.md#template-call) ), que se pueden utilizar para modular el código de los proyectos.

**¿Cuáles son los principales beneficios de HTL sobre JSP?**La seguridad y la eficiencia del proyecto son los principales beneficios, que se detallan en [Información general](overview.md).

**¿JSP eventualmente va?**En la fecha actual, no hay planes en estas líneas.

## Conceptos básicos de HTL {#fundamental-concepts-of-htl}

El lenguaje de plantilla HTML utiliza un lenguaje de expresión para insertar fragmentos de contenido en los elementos de marcado procesados y los atributos de datos HTML 5 para definir sentencias sobre bloques de marcado (como condiciones o iteraciones). Como HTL se compila en Java Servlets, las expresiones y los atributos de datos HTL se evalúan totalmente en el servidor y no se ve nada en el HTML resultante.

### Bloques y expresiones {#blocks-and-expressions}

Este es un primer ejemplo que puede estar contenido en un **`template.html`** archivo:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Se puede distinguir dos tipos de sintaxis diferentes:

* **[Bloquear declaraciones](block-statements.md)**
para mostrar condicionalmente la **& amp; lt; h 1 & amp; gt;** , se utiliza un atributo de datos `[data-sly-test](block-statements.md#test)` HTML 5. HTL proporciona varios atributos de este tipo, que permiten adjuntar comportamiento a cualquier elemento HTML y con el prefijo `data-sly`.

* **[Expresiones HTL de lenguaje](expression-language.md)**
de expresiones se delimitan por caracteres `${` y `}`. Durante la ejecución, se evalúan estas expresiones y su valor se inserta en el flujo HTML saliente.

Las dos páginas vinculadas arriba proporcionan la lista detallada de funciones disponibles para la sintaxis.

### Elemento SLY {#the-sly-element}

>[!NOTE]
>
>El elemento SHARP se ha introducido con AEM 6.1 o HTL 1.1.
>
>Antes de esto, se `[data-sly-unwrap](block-statements.md)` debía utilizar el atributo en su lugar.

Un concepto central de HTL es ofrecer la posibilidad de reutilizar elementos HTML existentes para definir sentencias de bloque, lo que evita la necesidad de insertar delimitadores adicionales para definir dónde comienza y finaliza la declaración. Esta anotación no obvia del marcado para transformar un HTML estático en una plantilla dinámica de funcionamiento ofrece la ventaja de no romper la validez del código HTML y, por lo tanto, mostrar correctamente, incluso como archivos estáticos.

Sin embargo, a veces no hay ningún elemento existente en la ubicación exacta donde se deba insertar una sentencia block. Para estos casos, es posible insertar un elemento EPS especial que se eliminará automáticamente del resultado, al ejecutar las declaraciones de bloque adjuntadas y mostrando su contenido en consecuencia.

Por ejemplo, siguiente ejemplo:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

devolverá algo similar a HTML, pero solo si hay ambos, a **`jcr:title`** y **`jcr:decription`** una propiedad definida, y si ninguno de ellos está vacío:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Una cosa que hay que tener cuidado es utilizar sólo el elemento SHARP cuando no se haya podido anotar ningún elemento existente con la sentencia block, porque los elementos SLY disuaden el valor ofrecido por el idioma para no alterar el HTML estático al hacerlo dinámico.

Por ejemplo, si el ejemplo anterior se hubiera envuelto ya dentro de un elemento DIV, el elemento EPS agregado sería abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

y el elemento DIV pudo haberse anotado con la condición:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Comentarios HTL {#htl-comments}

El siguiente ejemplo muestra en **la línea 1** un comentario HTL y en **la línea 2** un comentario HTML:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Los comentarios HTL son comentarios HTML con una sintaxis adicional de JavaScript. Todo el comentario HTL, y todo lo incluido en el mismo será omitido por el procesador y eliminado de la salida.

Se evaluará el contenido de los comentarios HTML estándar y se evaluarán las expresiones dentro del comentario.

Los comentarios HTML no pueden contener comentarios HTL y viceversa.

### Contextos especiales {#special-contexts}

Para poder sacar el mejor provecho de HTL, es importante comprender bien las consecuencias de estar basadas en la sintaxis HTML.

### Nombres de elementos y atributos {#element-and-attribute-names}

Las expresiones sólo pueden colocarse en texto HTML o valores de atributos, pero no en nombres de elementos ni nombres de atributos, o ya no serán códigos HTML válidos. Para configurar dinámicamente los nombres de elementos, la [`data-sly-element`](block-statements.md#element) sentencia se puede utilizar en los elementos que desee y para definir nombres de atributo dinámicamente, incluso configurar varios atributos a la vez, se [`data-sly-attribute`](block-statements.md#attribute) puede utilizar la sentencia.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sin sentencias de bloque {#contexts-without-block-statements}

Como HTL utiliza atributos de datos para definir sentencias de bloque, no es posible definir dichas afirmaciones de bloque dentro de los contextos siguientes y solo se pueden utilizar expresiones:

* Comentarios HTML
* Elementos de secuencia de comandos
* Elementos de estilo

El motivo es que el contenido de estos contextos es texto y no HTML, y que los elementos HTML que contiene se considerarían datos de caracteres simples. Por lo tanto, sin elementos HTML reales, tampoco se pueden ejecutar **`data-sly`** atributos.

Esto puede parecer una gran restricción, pero es un método deseado, ya que el lenguaje de plantilla HTML no debe utilizarse para generar resultados que no sean HTML. La sección [Usar API para acceder a lógica de lógica](getting-started.md#use-api-for-accessing-logic) muestra cómo se puede llamar a una lógica adicional desde la plantilla, que se puede utilizar si se necesita para preparar resultados complejos para estos contextos. Por ejemplo, una manera sencilla de enviar datos desde el back-end a una secuencia de comandos de front-end consiste en tener la lógica del componente para generar una cadena JSON, que luego se puede colocar en un atributo de datos con una expresión HTL sencilla.

El siguiente ejemplo ilustra el comportamiento de los comentarios HTML, pero en los elementos de estilo o secuencia de comandos se observa el mismo comportamiento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

generará algo similar a HTML siguiente:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Se requieren contextos explícitos {#explicit-contexts-required}

Como se explica en [la](getting-started.md#automatic-context-aware-escaping) sección Escaping según el contexto automático, un objetivo de HTL es reducir los riesgos de introducir vulnerabilidades de secuencias de comandos entre sitios (XSS) aplicando automáticamente el contexto que reconoce el contexto a todas las expresiones. Aunque HTL puede detectar automáticamente el contexto de expresiones colocadas dentro del marcado HTML, no analiza la sintaxis de JavaScript o CSS en línea, y por lo tanto se basa en el desarrollador para especificar explícitamente qué contexto exacto debe aplicarse a dichas expresiones.

Dado que no aplicar los resultados de escape correctos en vulnerabilidades XSS, HTL elimina la salida de todas las expresiones que están en los contextos de secuencia de comandos y estilo cuando el contexto no se ha declarado.

Este es un ejemplo de cómo definir el contexto para expresiones colocadas dentro de scripts y estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para obtener más información sobre cómo controlar la escapadora, consulte la sección [Contexto](expression-language.md#display-context) de visualización de idioma de expresión.

### Limitaciones de eliminación de contextos especiales {#lifting-limitations-of-special-contexts}

En los casos especiales en los que se necesita evitar las restricciones de los contextos de secuencia de comandos, estilo y comentario, es posible aislar su contenido en un archivo HTL independiente. Todo lo que esté ubicado en su propio archivo será interpretado por HTL como fragmento HTML normal, olvidando el contexto delimitado desde el cual se puede haber incluido.

Consulte la sección [Trabajar con plantillas](getting-started.md#working-with-client-side-templates) del lado del cliente hacia abajo para ver un ejemplo.

>[!CAUTION]
>
>Esta técnica puede introducir vulnerabilidades de secuencias de comandos entre sitios (XSS), y los aspectos de seguridad deben estudiarse cuidadosamente si se utiliza. Normalmente existen mejores maneras de implementar la misma cosa que confiar en esta práctica.

## Capacidades generales de HTL {#general-capabilities-of-htl}

Esta sección avanza rápidamente por las funciones generales del lenguaje de plantilla HTML.

### Uso de API para acceder a lógica {#use-api-for-accessing-logic}

Considere el siguiente ejemplo:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

Y después del `logic.js` archivo JavaScript ejecutado en el lado del servidor ubicado al lado:

```
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Como el lenguaje de plantilla HTML no permite mezclar código dentro del marcado, sino que ofrece en su lugar el mecanismo de extensión Usar API para ejecutar fácilmente el código de la plantilla.

El ejemplo anterior utiliza JavaScript ejecutado en el servidor para acortar el título a 10 caracteres, pero también podría haber utilizado el código Java para hacer lo mismo proporcionando un nombre de clase Java completo. Generalmente, la lógica comercial debe estar integrada en Java, pero cuando el componente necesita algunos cambios específicos de la vista de lo que proporciona la API de Java, resulta práctico utilizar algunos JavaScript ejecutados por el servidor para lograrlo.

Más información sobre esto en las siguientes secciones:

* La sección de la [afirmación de uso oscuro](block-statements.md#use) explica todo lo que se puede hacer con esa instrucción.
* La página [Usar API](use-api.md) proporciona información para elegir entre escribir la lógica en Java o JavaScript.
* Y para detallar cómo escribir la lógica, la [API de uso JavaScript](use-api-javascript.md) y las páginas [de Java Use-API](use-api-java.md) deben ayudarle.

### Escape automático según el contexto {#automatic-context-aware-escaping}

Considere el siguiente ejemplo:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

En la mayoría de los lenguajes de plantilla, este ejemplo podría crear una vulnerabilidad de secuencia de comandos entre sitios (XSS), ya que incluso cuando todas las variables se escapan automáticamente, el `href` atributo debe seguir siendo literalmente escapado URL. Esta omisión es uno de los errores más comunes, ya que se puede olvidar fácilmente y es difícil de identificar de forma automática.

Para contribuir a ello, el lenguaje de plantilla HTML escapa automáticamente cada variable de forma correspondiente al contexto en el que se coloca. Esto es posible gracias al hecho de que HTL comprende la sintaxis de HTML.

Asumiendo `logic.js` el siguiente archivo:

```
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

El ejemplo inicial dará como resultado una salida siguiente:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Observe cómo se escapó el atributo de forma diferente, ya que HTL sabe que `href` se deben aplicar caracteres de escape a los `src` atributos para el contexto de URI. Además, si el URI comenzó con **`javascript:`**, el atributo se habría eliminado completamente, a menos que el contexto se cambiara explícitamente a otra cosa.

Para obtener más información sobre cómo controlar la escapadora, consulte la sección [Contexto](expression-language.md#display-context) de visualización de idioma de expresión.

### Eliminación automática de atributos vacíos {#automatic-removal-of-empty-attributes}

Considere el siguiente ejemplo:

```xml
<p class="${properties.class}">some text</p>
```

Si el valor de `class` la propiedad parece estar vacío, el lenguaje de plantilla HTML quitará automáticamente todo `class` el atributo del resultado.

Esto es posible, ya que HTL comprende la sintaxis HTML y, por lo tanto, puede mostrar condicionalmente atributos con valores dinámicos únicamente si su valor no está vacío. Esto resulta extremadamente práctico ya que evita agregar un bloque de condición alrededor de los atributos, lo cual habría hecho que la marca no se pudiera leer y no se podía leer.

Además, el tipo de la variable ubicada en la expresión importa:

* **Cadena:**
   * **no está vacío:** Establece la cadena como valor de atributo.
   * **vacío:** Quita el atributo por completo.

* **Número:** Establece el valor como valor de atributo.

* **Booleano:**
   * **true:** Muestra el atributo sin valor (como un atributo HTML booleano)
   * **false:** Quita el atributo por completo.

Este es un ejemplo de cómo una expresión booleana permite controlar un atributo booleano HTML:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Para configurar los atributos, la [`data-sly-attribute`](block-statements.md#attribute) sentencia también puede resultar útil.

## Patrones comunes con HTL {#common-patterns-with-htl}

Esta sección presenta algunos escenarios comunes y cómo resolverlos mejor con el lenguaje de plantilla HTML.

### Cargando bibliotecas de cliente {#loading-client-libraries}

En HTL, las bibliotecas de cliente se cargan a través de una plantilla de ayuda proporcionada por AEM, a la que se puede acceder [`data-sly-use`](block-statements.md#use)desde. Hay tres plantillas disponibles en este archivo, a las que se puede llamar mediante [`data-sly-call`](block-statements.md#template-call):

* **`css`** - Carga únicamente los archivos CSS de las bibliotecas de cliente a las que se hace referencia.
* **`js`** - Carga únicamente los archivos JavaScript de las bibliotecas de cliente a las que se hace referencia.
* **`all`** - Carga todos los archivos de las bibliotecas de cliente a las que se hace referencia (tanto CSS como JavaScript).

Cada plantilla de ayuda espera una **`categories`** opción para hacer referencia a las bibliotecas de cliente deseadas. Esa opción puede ser una matriz de valores de cadena o una cadena que contenga una lista de valores separados por comas.

Estos son dos ejemplos cortos:

### Cargando varias bibliotecas de cliente por completo a la vez {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### Referencia a una biblioteca de cliente en diferentes secciones de una página {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

En el segundo ejemplo anterior, en caso de que el HTML **`head`** y **`body`** los elementos se coloquen en diferentes archivos, entonces la **`clientlib.html`** plantilla debería cargarse en cada archivo que lo necesite.

La sección de las [declaraciones de la plantilla y la llamada](block-statements.md#template-call) proporciona más detalles sobre cómo funcionan y las llamadas a estas plantillas.

### Pasar datos al cliente {#passing-data-to-the-client}

La manera más elegante y elegante de transmitir datos al cliente en general, pero aún más con HTL, es utilizar atributos de datos.

Ejemplo siguiente muestra cómo la lógica (que también se puede escribir en Java) se puede utilizar para serializar muy convenientemente a JSON el objeto que se va a pasar al cliente, que se puede colocar fácilmente en un atributo de datos:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

Desde allí, es fácil imaginar cómo un JavaScript de cliente puede acceder a ese atributo y analizar nuevamente el JSON. Esto sería por ejemplo el JavaScript correspondiente para colocarlo en una biblioteca de cliente:

```
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Uso de plantillas del lado del cliente {#working-with-client-side-templates}

Un caso especial, donde la técnica explicada en la sección Limitaciones [de Bing de contextos especiales](getting-started.md#lifting-limitations-of-special-contexts) puede usarse legítimamente, es escribir plantillas del lado del cliente (como Handlebars, por ejemplo) que se encuentran dentro de elementos **de secuencias de comandos** . La razón por la que esta técnica se puede utilizar con seguridad, se debe a que el elemento **de secuencia de comandos** no contiene JavaScript como se supone, sino más elementos HTML. Este es un ejemplo de cómo funcionaría:

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

Como se muestra arriba, la marca que se incluirá en **`script`** el elemento puede contener sentencias de bloque HTL y las expresiones no necesitan proporcionar contextos explícitos, ya que el contenido de la plantilla de Handlebars se ha aislado en su propio archivo. Asimismo, este ejemplo muestra cómo se puede mezclar HTL ejecutado en el servidor (como el **`h2`** elemento) con un lenguaje de plantilla ejecutado por parte del cliente, como Handlebars (se muestra en **`h3`** el elemento).

Sin embargo, una técnica más moderna sería utilizar el elemento HTML **`template`** en su lugar, ya que la ventaja sería que no requiere aislar el contenido de las plantillas en archivos separados.

**Siguiente:**

* [Idioma](expression-language.md) de expresión: para aprender en detalle lo que se puede hacer dentro de las expresiones HTL.
* [Sentencias](block-statements.md) de bloque: para descubrir todas las declaraciones de bloque disponibles en HTL y cómo utilizarlas.
