---
title: Introducción a HTL
seo-title: Introducción a HTL
description: HTL compatible con AEM sustituye a JSP como sistema de plantillas preferido y recomendado en el servidor para HTML en AEM.
seo-description: El lenguaje de plantilla HTML (HTL) admitido por Adobe Experience Manager sustituye a JSP como el sistema de plantillas preferido y recomendado en el servidor para HTML en AEM.
uuid: 4a7d6748-8cdf-4280-a85d-6c5319abf487
content-type: referencia
topic-tags: introducción
discoiquuid: 3bf2ca75-0d68-489d-bd1c-1d4fd730c61a
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: 1e3df6159b48da27460f3ad95c22c13d025b1a72

---


# Introducción a HTL {#getting-started-with-htl}

El lenguaje de plantilla HTML (HTL) admitido por Adobe Experience Manager (AEM) sustituye a JSP (JavaServer Pages) como sistema de plantillas preferido y recomendado en el servidor para HTML en AEM.

>[!NOTE]
>
>Para ejecutar la mayoría de los ejemplos que se proporcionan en esta página, se puede utilizar un entorno de ejecución activo denominado [Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) de impresión Eval de lectura.
>
>La comunidad de AEM ha generado una serie de [artículos, vídeos y seminarios web](related-community-articles.md) relacionados con el uso de HTL.

## HTL sobre JSP {#htl-over-jsp}

Se recomienda que los nuevos proyectos de AEM utilicen el lenguaje de plantilla HTML, ya que ofrece varias ventajas en comparación con JSP. Sin embargo, para los proyectos existentes, una migración sólo tiene sentido si se estima que es menos esfuerzo que mantener los JSP existentes para los próximos años.

Pero pasar a HTL no es necesariamente una opción de todo o nada, porque los componentes escritos en HTL son compatibles con los componentes escritos en JSP o ESP. Lo que significa que los proyectos existentes pueden sin problemas utilizar HTL para nuevos componentes, manteniendo JSP para los componentes existentes.

Incluso dentro del mismo componente, los archivos HTL se pueden utilizar junto con JSP y ESP. El siguiente ejemplo muestra en la **línea 1** cómo incluir un archivo HTL de un archivo JSP y en la **línea 2** cómo se puede incluir un archivo JSP de un archivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### Preguntas frecuentes{#frequently-asked-questions}

Antes de comenzar con el lenguaje de plantilla HTML, comencemos por responder por adelantado algunas preguntas relacionadas con el tema JSP vs HTL.

**¿HTL tiene limitaciones que JSP no tiene?**
HTL no tiene realmente limitaciones en comparación con JSP en el sentido de que lo que se puede hacer con JSP también debería ser alcanzable con HTL. Sin embargo, HTL es por diseño más estricto que JSP en varios aspectos, y lo que se puede lograr en un solo archivo JSP puede ser necesario separarlo en una clase Java o en un archivo JavaScript para poder alcanzarlo en HTL. Pero esto es lo que se desea generalmente para asegurar una buena separación de preocupaciones entre la lógica y el marcado.

**¿HTL admite las bibliotecas de etiquetas JSP?**
No, pero como se muestra en la sección [Cargando bibliotecas](getting-started.md#loading-client-libraries) de clientes, las sentencias [template &amp; call](block-statements.md#template-call) ofrecen un patrón similar.

**¿Se pueden ampliar las funciones de HTL en un proyecto de AEM?**
**No, pero como se muestra en la sección [Cargando bibliotecas](getting-started.md#loading-client-libraries) de clientes, las instrucciones de [plantilla y llamada](block-statements.md#template-call) ofrecen un patrón similar.
No, no pueden. HTL cuenta con potentes mecanismos de extensión para la reutilización de la lógica ( [Use-API](getting-started.md#use-api-for-accessing-logic) ) y del marcado ( [plantilla y declaraciones de llamada](block-statements.md#template-call) ), que pueden utilizarse para modular el código de los proyectos.

**¿Cuáles son los principales beneficios de HTL sobre JSP?**
La seguridad y la eficacia del proyecto son las principales ventajas, que se detallan en la [Visión General](overview.md).

**¿JSP se irá eventualmente?**
En la fecha actual, no hay planes en este sentido.

## Conceptos fundamentales de HTL {#fundamental-concepts-of-htl}

El lenguaje de plantilla HTML utiliza un lenguaje de expresión para insertar fragmentos de contenido en el marcado procesado y atributos de datos HTML5 para definir sentencias sobre bloques de marcado (como condiciones o iteraciones). A medida que HTL se compila en servlets de Java, las expresiones y los atributos de datos HTL se evalúan por completo en el servidor y nada permanece visible en el HTML resultante.

### Bloques y expresiones {#blocks-and-expressions}

A continuación se muestra un primer ejemplo, que puede estar contenido como está en un **`template.html`** archivo:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Se pueden distinguir dos tipos diferentes de sintaxis:

* **[Bloquear sentencias](block-statements.md)** Para mostrar condicionalmente el elemento **&lt;h1&gt;** , se utiliza un atributo de datos `[data-sly-test](block-statements.md#test)` HTML5. HTL proporciona varios atributos de este tipo, que permiten adjuntar el comportamiento a cualquier elemento HTML, y todos tienen el prefijo `data-sly`.

* **[Las expresiones de lenguaje](expression-language.md)** de expresión HTL están delimitadas por caracteres `${` y `}`. En tiempo de ejecución, estas expresiones se evalúan y su valor se inserta en el flujo HTML saliente.

Las dos páginas vinculadas arriba proporcionan la lista detallada de funciones disponibles para la sintaxis.

### El elemento SLY {#the-sly-element}

>[!NOTE]
>
>El elemento SLY se ha introducido con AEM 6.1 o HTL 1.1.
>
>Antes de eso, el `[data-sly-unwrap](block-statements.md)` atributo tenía que usarse en su lugar.

Un concepto central de HTL es ofrecer la posibilidad de reutilizar los elementos HTML existentes para definir sentencias de bloque, lo que evita la necesidad de insertar delimitadores adicionales para definir dónde comienza y termina la instrucción. Esta anotación discreta del marcado para transformar un HTML estático en una plantilla dinámica que funcione ofrece la ventaja de no romper la validez del código HTML y, por tanto, de seguir mostrándose correctamente, incluso como archivos estáticos.

Sin embargo, a veces puede que no haya un elemento existente en la ubicación exacta en la que se debe insertar una sentencia block. En estos casos, es posible insertar un elemento especial SLY que se eliminará automáticamente del resultado, mientras se ejecutan las sentencias de bloque adjuntas y se muestra su contenido en consecuencia.

Así que el siguiente ejemplo:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

generará algo como el siguiente HTML, pero solo si hay ambas, una **`jcr:title`** y una **`jcr:decription`** propiedad definidas y si ninguna de ellas está vacía:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Sin embargo, una cosa que hay que tener cuidado es usar solamente el elemento SLY cuando no se pudo haber anotado ningún elemento existente con la sentencia block, porque los elementos SLY disuaden el valor ofrecido por el idioma para no alterar el HTML estático al hacerlo dinámico.

Por ejemplo, si el ejemplo anterior ya se hubiera ajustado dentro de un elemento DIV, el elemento SLY añadido sería abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

y el elemento DIV podría haberse anotado con la condición:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Comentarios HTL {#htl-comments}

El siguiente ejemplo muestra en la **línea 1** un comentario HTL y, en la **línea 2** , un comentario HTML:

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Los comentarios HTML son comentarios HTML con una sintaxis adicional de tipo JavaScript. El procesador ignorará por completo todo el comentario HTL, y todo lo que haya en él, y se eliminará de la salida.

Sin embargo, el contenido de los comentarios HTML estándar se pasará y se evaluarán las expresiones dentro del comentario.

Los comentarios HTML no pueden contener comentarios HTL y viceversa.

### Contextos especiales {#special-contexts}

Para poder hacer el mejor uso de HTL, es importante comprender bien las consecuencias de que se base en la sintaxis HTML.

### Nombres de elementos y atributos {#element-and-attribute-names}

Las expresiones solo se pueden colocar en texto HTML o valores de atributos, pero no en nombres de elementos o nombres de atributos, o ya no serían HTML válido. Para configurar los nombres de elementos de forma dinámica, la [`data-sly-element`](block-statements.md#element) sentencia se puede utilizar en los elementos deseados y, para definir dinámicamente nombres de atributos, incluso estableciendo varios atributos a la vez, se puede utilizar la [`data-sly-attribute`](block-statements.md#attribute) sentencia.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sin sentencias de bloque {#contexts-without-block-statements}

Dado que HTL utiliza atributos de datos para definir sentencias de bloque, no es posible definir dichas sentencias de bloque dentro de los contextos siguientes, y solo se pueden usar expresiones allí:

* Comentarios HTML
* Elementos de secuencia de comandos
* Elementos de estilo

La razón es que el contenido de estos contextos es texto y no HTML, y los elementos HTML contenidos se considerarían datos de caracteres simples. Por lo tanto, sin elementos HTML reales, tampoco se pueden ejecutar **`data-sly`** atributos.

Esto puede sonar como una gran restricción, sin embargo es una que se desea, porque el lenguaje de plantilla HTML no debería utilizarse indebidamente para generar resultados que no sean HTML. La sección [Usar API para acceder a la lógica](getting-started.md#use-api-for-accessing-logic) que se muestra a continuación presenta cómo se puede llamar a la lógica adicional desde la plantilla, que se puede utilizar si es necesario para preparar resultados complejos para estos contextos. Por ejemplo, una manera fácil de enviar datos desde el back-end a un script front-end es tener la lógica del componente para generar una cadena JSON, que luego se puede colocar en un atributo de datos con una expresión HTL simple.

El siguiente ejemplo ilustra el comportamiento de los comentarios HTML, pero en elementos de estilo o de secuencia de comandos, se observa el mismo comportamiento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

generará algo como el siguiente HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextos explícitos requeridos {#explicit-contexts-required}

Como se explica en la sección Escapar [según el contexto](getting-started.md#automatic-context-aware-escaping) automático que aparece a continuación, un objetivo de HTL es reducir los riesgos de introducir vulnerabilidades de scripts entre sitios (XSS) mediante la aplicación automática de escapes según el contexto a todas las expresiones. Aunque HTL puede detectar automáticamente el contexto de expresiones colocadas dentro del marcado HTML, no analiza la sintaxis de JavaScript o CSS en línea y, por lo tanto, depende del programador para especificar explícitamente qué contexto exacto debe aplicarse a dichas expresiones.

Dado que no se aplican los resultados de escape correctos en vulnerabilidades XSS, HTL elimina, por tanto, la salida de todas las expresiones que están en contextos de estilo y secuencia de comandos cuando no se ha declarado el contexto.

A continuación se muestra un ejemplo de cómo establecer el contexto de las expresiones colocadas dentro de secuencias de comandos y estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para obtener más información acerca de cómo controlar el escape, consulte la sección [Lenguaje de visualización de contexto](expression-language.md#display-context) .

### Limitaciones de elevación de contextos especiales {#lifting-limitations-of-special-contexts}

En los casos especiales en los que es necesario evitar las restricciones de los contextos de script, estilo y comentario, es posible aislar su contenido en un archivo HTML independiente. HTL interpretará todo lo que se encuentre en su propio archivo como un fragmento HTML normal, olvidando el contexto limitante desde el cual podría haberse incluido.

Consulte la sección [Uso de plantillas](getting-started.md#working-with-client-side-templates) de cliente más abajo para ver un ejemplo.

>[!CAUTION]
>
>Esta técnica puede introducir vulnerabilidades de scripts entre sitios (XSS), y los aspectos de seguridad deben estudiarse cuidadosamente si se utiliza. Generalmente hay mejores maneras de implementar lo mismo que confiar en esta práctica.

## Capacidades generales de HTL {#general-capabilities-of-htl}

Esta sección explora rápidamente las funciones generales del lenguaje de plantillas HTML.

### Use-API para acceder a la lógica {#use-api-for-accessing-logic}

Considere el siguiente ejemplo:

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

Y el siguiente archivo JavaScript ejecutado por `logic.js` el servidor ubicado junto a él:

```
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

Dado que el lenguaje de plantilla HTML no permite mezclar código dentro del marcado, ofrece en su lugar el mecanismo de extensión Use-API para ejecutar código fácilmente desde la plantilla.

El ejemplo anterior utiliza JavaScript ejecutado en el servidor para acortar el título a 10 caracteres, pero también podría haber utilizado código Java para hacer lo mismo proporcionando un nombre de clase Java completo. Generalmente, la lógica empresarial debería ser más bien creada en Java, pero cuando el componente necesita algunos cambios específicos de la vista de lo que proporciona la API de Java, puede ser conveniente usar algún JavaScript ejecutado en el servidor para hacerlo.

Más información sobre esto en las siguientes secciones:

* En la sección de la instrucción [](block-statements.md#use) data-astre-use se explica todo lo que se puede hacer con esa afirmación.
* La página [](use-api.md) Use-API proporciona cierta información para ayudarle a elegir entre escribir la lógica en Java o en JavaScript.
* Y para detallar cómo escribir la lógica, las páginas Use-API [de](use-api-javascript.md) JavaScript y Use-API [de](use-api-java.md) Java deberían ayudar.

### Escapado según el contexto automático {#automatic-context-aware-escaping}

Considere el siguiente ejemplo:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

En la mayoría de los lenguajes de plantilla, este ejemplo podría crear una vulnerabilidad de secuencia de comandos entre sitios (XSS), ya que incluso cuando todas las variables se escapan automáticamente a HTML, el `href` atributo debe seguir siendo específicamente de escape de URL. Esta omisión es uno de los errores más comunes, ya que se puede olvidar muy fácilmente y es difícil detectarla de manera automatizada.

Para ayudarle, el lenguaje de plantilla HTML automáticamente escapa cada variable según el contexto en el que se coloca. Esto es posible gracias a que HTL entiende la sintaxis de HTML.

Suponiendo el siguiente `logic.js` archivo:

```
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

El ejemplo inicial dará como resultado el siguiente resultado:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Observe cómo los dos atributos se han escapado de forma diferente, ya que HTL sabe que `href` y `src` los atributos deben omitirse para el contexto URI. Además, si la URI empezara con **`javascript:`**, el atributo se habría eliminado por completo, a menos que el contexto se cambiara explícitamente a otro.

Para obtener más información acerca de cómo controlar el escape, consulte la sección [Lenguaje de visualización de contexto](expression-language.md#display-context) .

### Eliminación automática de atributos vacíos {#automatic-removal-of-empty-attributes}

Considere el siguiente ejemplo:

```xml
<p class="${properties.class}">some text</p>
```

Si el valor de la `class` propiedad está vacío, el lenguaje de plantilla HTML quitará automáticamente todo el `class` atributo de la salida.

De nuevo, esto es posible, ya que HTL comprende la sintaxis HTML y, por tanto, puede mostrar condicionalmente atributos con valores dinámicos solo si su valor no está vacío. Esto resulta extremadamente práctico, ya que evita agregar un bloque de condición alrededor de los atributos, lo que habría hecho que la marca no fuera válida y no se pudiera leer.

Además, el tipo de variable colocada en la expresión importa:

* **Cadena:**
   * **** no vacío: Establece la cadena como valor de atributo.
   * **** vacío: Elimina el atributo por completo.

* **** Número: Establece el valor como valor de atributo.

* **Booleano:**
   * **** true: Muestra el atributo sin valor (como un atributo HTML booleano)
   * **** false: Elimina el atributo por completo.

A continuación se muestra un ejemplo de cómo una expresión booleana permitiría controlar un atributo HTML booleano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Para configurar atributos, la [`data-sly-attribute`](block-statements.md#attribute) sentencia también puede ser útil.

## Patrones comunes con HTL {#common-patterns-with-htl}

Esta sección presenta algunos escenarios comunes y la mejor manera de resolverlos con el lenguaje de plantilla HTML.

### Cargando bibliotecas de cliente {#loading-client-libraries}

En HTL, las bibliotecas de cliente se cargan mediante una plantilla de ayuda proporcionada por AEM, a la que se puede acceder a través de [`data-sly-use`](block-statements.md#use). Hay tres plantillas disponibles en este archivo, a las que se puede llamar mediante [ `data-sly-call`](block-statements.md#template-call):

* **`css`** - Carga únicamente los archivos CSS de las bibliotecas de cliente a las que se hace referencia.
* **`js`** - Carga únicamente los archivos JavaScript de las bibliotecas de cliente a las que se hace referencia.
* **`all`** - Carga todos los archivos de las bibliotecas de cliente a las que se hace referencia (tanto CSS como JavaScript).

Cada plantilla de ayuda espera una **`categories`** opción para hacer referencia a las bibliotecas de cliente deseadas. Esa opción puede ser una matriz de valores de cadena o una cadena que contenga una lista de valores separados por comas.

Estos son dos ejemplos breves:

### Carga de varias bibliotecas de cliente a la vez {#loading-multiple-client-libraries-fully-at-once}

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

En el segundo ejemplo anterior, en caso de que el HTML **`head`** y los **`body`** elementos se coloquen en diferentes archivos, la **`clientlib.html`** plantilla tendría que cargarse en cada archivo que lo necesite.

La sección de las instrucciones de llamada [y](block-statements.md#template-call) plantilla proporciona más detalles sobre cómo funcionan la declaración y la llamada de estas plantillas.

### Pasar datos al cliente {#passing-data-to-the-client}

La mejor y más elegante manera de pasar datos al cliente en general, pero aún más con HTL, es utilizar atributos de datos.

En el siguiente ejemplo se muestra cómo se puede utilizar la lógica (que también se puede escribir en Java) para serializar de forma muy conveniente en JSON el objeto que se va a pasar al cliente, que se puede colocar fácilmente en un atributo de datos:

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

A partir de ahí, es fácil imaginar cómo un JavaScript de cliente puede acceder a ese atributo y volver a analizar el JSON. Por ejemplo, sería el JavaScript correspondiente que se colocaría en una biblioteca de cliente:

```
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Uso de plantillas de cliente {#working-with-client-side-templates}

Un caso especial, en el que la técnica explicada en la sección Limitaciones [de elevación de contextos](getting-started.md#lifting-limitations-of-special-contexts) especiales puede utilizarse legítimamente, es escribir plantillas del lado del cliente (como Handlebars, por ejemplo) que se encuentran dentro de elementos de **secuencias de comandos** . La razón por la que esta técnica se puede utilizar de forma segura en ese caso es porque el elemento de **secuencia de comandos** no contiene JavaScript como se supone, sino elementos HTML adicionales. Aquí hay un ejemplo de cómo funcionaría:

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

Como se muestra arriba, el marcado que se incluirá en el **`script`** elemento puede contener sentencias de bloque HTL y las expresiones no necesitan proporcionar contextos explícitos, ya que el contenido de la plantilla Handlebars se ha aislado en su propio archivo. Asimismo, este ejemplo muestra cómo se puede combinar HTL ejecutado en el lado del servidor (como en el **`h2`** elemento) con un lenguaje de plantilla ejecutado en el lado del cliente, como Handlebars (que se muestran en el **`h3`** elemento).

Sin embargo, una técnica más moderna sería utilizar el **`template`** elemento HTML en su lugar, ya que la ventaja sería que no es necesario aislar el contenido de las plantillas en archivos independientes.

**Lea lo siguiente:**

* [Lenguaje](expression-language.md) de expresión: para aprender en detalle qué se puede hacer dentro de las expresiones HTML.
* [Bloquear sentencias](block-statements.md) : para descubrir todas las sentencias de bloque disponibles en HTL y cómo utilizarlas.
