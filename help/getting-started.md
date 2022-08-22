---
title: Introducción a HTL
description: Obtenga información sobre HTL, el sistema de plantillas preferido y recomendado del lado del servidor para el HTML en AEM, y comprenda los conceptos principales del idioma y sus construcciones fundamentales.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '2170'
ht-degree: 56%

---


# Introducción a HTL {#getting-started-with-htl}

El lenguaje de plantilla de HTML (HTL) es el sistema de plantillas del lado del servidor recomendado para el HTML en Adobe Experience Manager. Como en todos los sistemas de creación de plantillas del lado del servidor de HTML, un archivo HTL define la salida enviada al explorador especificando el HTML en sí, algunas lógicas de presentación básicas y las variables que se evaluarán durante la ejecución.

Este documento ofrece una visión general del propósito de HTL, así como una introducción a conceptos y construcciones fundamentales del lenguaje.

>[!TIP]
>
>Este documento presenta el propósito de HTL y una visión general de su estructura y conceptos fundamentales. Si tiene preguntas sobre sintaxis específica, consulte la [especificación HTL.](specification.md)

## Capas HTL {#layers}

HTL como se utiliza en AEM puede definirse por varias capas.

1. **[Especificación HTL](specification.md)** - HTL es una especificación de código abierto, no basada en plataformas, que cualquiera puede implementar libremente.
1. **[Motor de secuencias de comandos HTL de Sling](specification.md)** - El proyecto Sling ha creado la implementación de referencia de HTL, que AEM utiliza.
1. **[Extensiones de AEM](specification.md)** : AEM se basa en el motor de secuencias de comandos HTL de Sling para ofrecer a los desarrolladores funciones prácticas específicas de AEM.

Esta documentación de HTL se centra en el uso de HTL para desarrollar soluciones de AEM. Como tal, afecta a las tres capas, vinculando los recursos externos según sea necesario.

## Conceptos fundamentales de HTL {#fundamental-concepts-of-htl}

El lenguaje de plantilla HTML utiliza un lenguaje de expresión para insertar fragmentos de contenido en el marcado procesado y los atributos de datos HTML5 para definir instrucciones sobre bloques de marcado (como condiciones o iteraciones). A medida que HTL se compila en Servlets Java, las expresiones y los atributos de datos HTL se evalúan completamente en el lado del servidor y nada permanece visible en el HTML resultante.

>[!TIP]
>
>Para ejecutar la mayoría de los ejemplos proporcionados en esta página, se puede utilizar un entorno de ejecución en directo denominado [Read Eval Print Loop](https://github.com/adobe/aem-htl-repl).

### Bloques y expresiones {#blocks-and-expressions}

Este es un primer ejemplo, que se puede contener tal y como está en un archivo `template.html`:

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

Se pueden distinguir dos tipos diferentes de sintaxis:

* **Declaraciones de bloque** - Para mostrar condicionalmente el `<h1>` elemento, `data-sly-test` Se utiliza el atributo de datos HTML5. HTL proporciona varios atributos de este tipo, que permiten adjuntar el comportamiento a cualquier elemento HTML, y todos tienen el prefijo `data-sly`.
* **Lenguaje de expresión** - Las expresiones HTL están delimitadas por la variable `${` y `}` caracteres. Durante el tiempo de ejecución, estas expresiones se evalúan y su valor se inserta en el flujo HTML saliente.

Consulte la [Especificación HTL](specification.md) para obtener más información sobre ambas sintaxis.

### El elemento SLY {#the-sly-element}

Un concepto central de HTL es ofrecer la posibilidad de reutilizar elementos HTML existentes para definir instrucciones de bloque, lo que evita la necesidad de insertar delimitadores adicionales para definir dónde comienza y finaliza la instrucción. Esta anotación discreta del marcado para transformar un HTML estático en una plantilla dinámica que funcione ofrece la ventaja de no romper la validez del código HTML y, por lo tanto, de seguir mostrándose correctamente, incluso como archivos estáticos.

Sin embargo, a veces puede que no haya un elemento existente en la ubicación exacta en la que se debe insertar una instrucción de bloque. Para tales casos, es posible insertar un `sly` elemento que se eliminará automáticamente de la salida, mientras se ejecutan las instrucciones de bloque adjuntas y se muestra su contenido en consecuencia.

El siguiente ejemplo...

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...generará algo como el siguiente HTML, pero solo si hay ambas, una propiedad `jcr:title` y una propiedad `jcr:description` definidas, y si ninguna de ellas está vacía:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Una cosa que hay que tener en cuenta es usar la variable `sly` elemento cuando no se pudo haber anotado ningún elemento existente con la instrucción block. Esto se debe a que `sly` los elementos disuaden al valor ofrecido por el idioma para no alterar el HTML estático al hacerlo dinámico.

Por ejemplo, si el ejemplo anterior se hubiera ajustado dentro de un `div` elemento y, a continuación, el elemento añadido `sly` sería abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

y `div` se puede haber anotado el elemento con la condición :

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Comentarios de HTL {#htl-comments}

El siguiente ejemplo muestra un comentario HTL en la primera línea y un comentario HTML en la segunda línea.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Los comentarios HTL son comentarios HTML con una sintaxis adicional de tipo JavaScript. El procesador ignorará por completo todo el comentario HTL, y todo lo que haya dentro, y lo quitará de la salida.

Sin embargo, el contenido de los comentarios HTML estándar se transmitirá y se evaluarán las expresiones dentro del comentario.

Los comentarios HTML no pueden contener comentarios HTL y viceversa.

### Contextos especiales {#special-contexts}

Para sacar el máximo partido a HTL, es importante entender bien las consecuencias de estar basado en la sintaxis HTML.

Consulte la [Mostrar sección de contexto](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) de la especificación HTL para obtener más información.

### Nombres de elementos y atributos {#element-and-attribute-names}

Las expresiones solo se pueden colocar en texto HTML o valores de atributo, pero no dentro de nombres de elemento o nombres de atributo, o ya no serían HTML válidos. Para establecer nombres de elementos de forma dinámica, se puede utilizar la instrucción `data-sly-element` en los elementos deseados y para establecer nombres de atributos de forma dinámica, incluso estableciendo varios atributos a la vez, se puede utilizar la instrucción `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sin instrucciones de bloque {#contexts-without-block-statements}

Como HTL utiliza atributos de datos para definir instrucciones de bloque, no es posible definir dichas instrucciones de bloque dentro de los siguientes contextos y solo se pueden utilizar expresiones en ellos:

* Comentarios HTML
* Elementos de script
* Elementos de estilo

El motivo es que el contenido de estos contextos es texto y no HTML, y los elementos HTML contenidos se considerarían como datos de caracteres simples. Por lo tanto, sin elementos HTML reales, tampoco puede haber atributos `data-sly` ejecutados.

Esto puede parecer una restricción significativa, pero es una que se desea, ya que el lenguaje de plantilla de HTML no debería usarse incorrectamente para generar resultados que no sean HTML. La sección [API de uso para la lógica de acceso](#use-api-for-accessing-logic) que aparece a continuación presenta cómo se puede llamar a la lógica adicional desde la plantilla, que se puede utilizar si es necesaria para preparar resultados complejos para estos contextos. Por ejemplo, una manera sencilla de enviar datos desde el back-end a un script front-end es tener la lógica del componente para generar una cadena JSON, que luego se puede colocar en un atributo de datos con una expresión HTL simple.

El siguiente ejemplo ilustra el comportamiento de los comentarios HTML, pero en los elementos de script o estilo, se observaría el mismo comportamiento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

generará algo similar al siguiente HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextos explícitos requeridos {#explicit-contexts-required}

Como se explica en la sección [Escape automático según el contexto](#automatic-context-aware-escaping) que aparece a continuación, uno de los objetivos de HTL es reducir los riesgos de introducir vulnerabilidades de scripts entre sitios (XSS) mediante la aplicación automática de escape según el contexto a todas las expresiones. Aunque HTL puede detectar automáticamente el contexto de las expresiones colocadas dentro del marcado HTML, no analiza la sintaxis de JavaScript o CSS en línea y, por lo tanto, depende del desarrollador para especificar explícitamente qué contexto exacto debe aplicarse a dichas expresiones.

Dado que no se aplican los resultados de escape correctos en vulnerabilidades XSS, HTL elimina la salida de todas las expresiones que están en contextos de script y estilo cuando no se ha declarado el contexto.

A continuación, se muestra un ejemplo de cómo establecer el contexto para las expresiones colocadas dentro de secuencias de comandos y estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para obtener más información sobre cómo controlar el escape, consulte la sección [Contexto de visualización del idioma de expresión](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) de las especificaciones HTL.

## Capacidades generales de HTL {#general-capabilities-of-htl}

Esta sección muestra rápidamente las funciones generales del lenguaje de plantilla HTML.

### API de uso para acceder a la lógica {#use-api-for-accessing-logic}

Lenguaje de plantilla HTML - HTL - La API para uso de Java permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada mediante `data-sly-use`. Esto permite encapsular toda la lógica empresarial compleja en el código Java, mientras que el código HTL solo trata la producción de marcado directo.

Consulte el documento [HTL Java Use-API](java-use-api.md) para obtener más información.

### Escape automático según el contexto {#automatic-context-aware-escaping}

Consideremos el siguiente ejemplo:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

En la mayoría de los lenguajes de plantilla, este ejemplo podría crear una vulnerabilidad de ejecución de scripts en sitios cruzados (XSS), ya que incluso cuando todas las variables se escapan de forma automática de HTML, el atributo `href` debe seguir siendo específicamente de escape de URL. Esta omisión es uno de los errores más comunes, ya que se puede olvidar fácilmente, y es difícil detectarla de manera automatizada.

Para ayudarle con esto, el lenguaje de plantilla HTML omite automáticamente cada variable según el contexto en el que se coloca. Esto es posible gracias al hecho de que HTL entiende la sintaxis del HTML.

Con el siguiente archivo `logic.js` como ejemplo:

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

El ejemplo inicial generará el siguiente resultado:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Observe cómo se omitieron los dos atributos de forma diferente, porque HTL sabe que `href` y `src` para el contexto URI, se deben escapar los atributos de . Además, si la URI empezara por `javascript:`, el atributo se habría eliminado por completo, a menos que el contexto se cambiara explícitamente a otro.

Para obtener más información sobre cómo controlar el escape, consulte la sección [Contexto de visualización del idioma de expresión](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) de las especificaciones HTL.

### Eliminación automática de atributos vacíos {#automatic-removal-of-empty-attributes}

Consideremos el siguiente ejemplo:

```xml
<p class="${properties.class}">some text</p>
```

Si el valor de la propiedad `class` está vacío, el lenguaje de plantilla HTML quitará automáticamente todo el atributo `class` de la salida.

De nuevo, esto es posible, ya que HTL comprende la sintaxis del HTML y, por lo tanto, puede mostrar condicionalmente atributos con valores dinámicos solo si su valor no está vacío. Esto resulta muy práctico, ya que evita añadir un bloque de condición alrededor de los atributos, lo que habría hecho que el marcado no fuera válido e ilegible.

Además, el tipo de variable colocada en la expresión es importante:

* **String:**
   * **not empty:** establece la cadena como valor de atributo.
   * **empty:** quita el atributo por completo.

* **Number:** establece el valor como valor de atributo.

* **Boolean:**
   * **true:** muestra el atributo sin valor (como atributo HTML booleano)
   * **false:** elimina completamente el atributo.

Este es un ejemplo de cómo una expresión booleana permitiría el control de un atributo de HTML booleano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Para configurar atributos, la instrucción `data-sly-attribute` también puede ser útil.

## Patrones comunes con HTL {#common-patterns-with-htl}

Esta sección presenta algunos escenarios comunes y cómo solucionarlos mejor con el lenguaje de plantilla HTML.

### Carga de bibliotecas de cliente {#loading-client-libraries}

En HTL, las bibliotecas de cliente se cargan a través de una plantilla de ayuda proporcionada por AEM, a la que se puede acceder mediante `data-sly-use`. Hay tres plantillas disponibles en este archivo a las que se puede llamar mediante `data-sly-call`:

* **`css`**: carga solo los archivos CSS de las bibliotecas de cliente a las que se hace referencia.
* **`js`**: carga solo los archivos JavaScript de las bibliotecas de cliente a las que se hace referencia.
* **`all`**: carga todos los archivos de las bibliotecas de cliente a las que se hace referencia (tanto CSS como JavaScript).

Cada plantilla de ayuda espera una opción `categories` para hacer referencia a las bibliotecas de cliente deseadas. Esa opción puede ser una matriz de valores de cadena o una cadena que contenga una lista de valores separados por comas.

Los siguientes son dos ejemplos breves.

#### Carga completa de varias bibliotecas de cliente a la vez {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### Hacer referencia a una biblioteca de cliente en diferentes secciones de una página {#referencing-a-client-library-in-different-sections-of-a-page}

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

En este ejemplo, en caso de que el HTML `head` y `body` los elementos se colocan en diferentes archivos, la variable `clientlib.html` luego, la plantilla tendría que cargarse en cada archivo que la necesite.

La sección sobre la plantilla y las instrucciones de llamada en la [Especificación HTL](specification.md) proporciona más detalles sobre cómo funcionan las plantillas de declaración y de llamada.

### Pasar datos al cliente {#passing-data-to-the-client}

La mejor y más elegante manera de pasar datos al cliente en general, pero aún más con HTL, es usar `data` atributos.

El siguiente ejemplo muestra cómo se puede utilizar la lógica (que también se puede escribir en Java) para serializar convenientemente en JSON el objeto que se va a pasar al cliente, que luego se puede colocar fácilmente en un `data` atributo:

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
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

A partir de ahí, es fácil imaginar cómo un JavaScript del lado del cliente puede acceder a ese atributo y volver a analizar el JSON. Este sería, por ejemplo, el JavaScript correspondiente que se colocará en una biblioteca de cliente:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Uso de plantillas del lado del cliente {#working-with-client-side-templates}

Un caso especial, en el que la técnica explicada en la sección [Limitaciones de elevación de contextos especiales](#lifting-limitations-of-special-contexts) puede utilizarse legítimamente, es escribir plantillas del lado del cliente (como Handlebars por ejemplo) que se encuentran dentro de elementos `scrip`. La razón por la que esta técnica se puede utilizar de forma segura en ese caso es porque el elemento `script` no contiene JavaScript como se supone, sino más elementos HTML. Aquí hay un ejemplo de cómo funcionaría:

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

Como se muestra más arriba, el marcado que se incluirá en la variable `script` puede contener instrucciones de bloque HTL y las expresiones no necesitan proporcionar contextos explícitos, ya que el contenido de la plantilla Handlebars se ha aislado en su propio archivo. Además, este ejemplo muestra cómo se puede mezclar HTL ejecutado en el lado del servidor (como en el elemento `h2` ) con un lenguaje de plantilla ejecutado en el lado del cliente, como Handlebars (mostrado en el elemento `h3` ).

Sin embargo, una técnica más moderna sería utilizar el elemento HTML `template` en su lugar, ya que la ventaja sería que no es necesario aislar el contenido de las plantillas en archivos separados.

### Limitaciones de elevación de contextos especiales {#lifting-limitations-of-special-contexts}

En los casos especiales en los que es necesario evitar las restricciones de los contextos de script, estilo y comentario, es posible aislar su contenido en un archivo HTL independiente. HTL interpretará todo lo que se encuentre en su propio archivo como un fragmento HTML normal, olvidando el contexto limitante desde el cual podría haberse incluido.

Consulte la sección [Trabajo con plantillas del lado del cliente](#working-with-client-side-templates) más abajo para ver un ejemplo.

>[!CAUTION]
>
>Esta técnica puede introducir vulnerabilidades de scripts entre sitios (XSS), y los aspectos de seguridad deben estudiarse cuidadosamente si es necesario utilizarlos. Por lo general, hay mejores maneras de implementar lo mismo que confiar en esta práctica.
