---
title: Introducción a HTL
description: Obtenga información acerca de HTL, el sistema de plantillas preferido y recomendado del lado del servidor para el HTML en AEM, y comprenda los conceptos principales del lenguaje y sus construcciones fundamentales.
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: ht
source-wordcount: '2045'
ht-degree: 100%

---


# Introducción a HTL {#getting-started-with-htl}

El lenguaje de plantilla HTML (HTL) es el sistema de plantillas del lado del servidor recomendado para HTML en Adobe Experience Manager. Como en todos los sistemas de plantillas del lado del servidor HTML, un archivo HTL define la salida enviada al explorador especificando el propio HTML, alguna lógica de presentación básica y variables que se deben evaluar durante la ejecución.

Este documento ofrece una visión general del propósito de HTL, así como una introducción a conceptos y construcciones fundamentales del lenguaje.

>[!TIP]
>
>Este documento presenta el propósito de HTL y una visión general de su estructura y conceptos fundamentales. Si tiene preguntas sobre la sintaxis específica, consulte la [especificación de HTL](specification.md).

## Capas HTL {#layers}

En AEM, un número de capas definen definen HTL.

1. **[Especificación de HTL](specification.md)**: HTL es una especificación de código abierto que no se basa en plataformas y cualquiera puede implementar libremente.
1. **[`Sling`Motor de scripts HTL](specification.md)**: el proyecto `Sling` ha creado la implementación de referencia de HTL, que utiliza AEM.
1. **[Extensiones de AEM](specification.md)**: AEM se basa en el motor de scripts HTL de `Sling` para ofrecer a los desarrolladores funciones específicas de AEM.

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

* **Instrucciones de bloque**: si desea mostrar el elemento `<h1>` de forma condicional, utilice un atributo de datos HTML5 `data-sly-test`. HTL proporciona varios atributos de este tipo, que permiten adjuntar el comportamiento a cualquier elemento HTML, y todos tienen el prefijo `data-sly`.
* **Lenguaje de expresión**: los caracteres `${` y `}` delimitan las expresiones HTL. Durante el tiempo de ejecución, estas expresiones se evalúan y su valor se inserta en el flujo HTML saliente.

Consulte la [Especificación de HTL](specification.md) para obtener más información sobre ambas sintaxis.

### El elemento SLY {#the-sly-element}

Un concepto central de HTL es ofrecer la posibilidad de reutilizar elementos HTML existentes para definir instrucciones de bloque. Esta reutilización evita la necesidad de insertar delimitadores adicionales para definir dónde comienza y finaliza la instrucción. La anotación del marcado transforma discretamente el HTML estático en una plantilla dinámica sin romper la validez del HTML, garantizando una visualización adecuada, incluso como archivos estáticos.

Sin embargo, a veces puede que no haya un elemento existente en la ubicación exacta en la que se debe insertar una instrucción de bloque. En estos casos, puede insertar un elemento `sly` especial. Este elemento se elimina automáticamente de la salida mientras se ejecutan las instrucciones de bloque adjuntas y se muestra su contenido en consecuencia.

El siguiente ejemplo:

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

Genera algo parecido al siguiente HTML, pero solo si hay definidas tanto una propiedad `jcr:title` como una propiedad `jcr:description`, y si ninguna de ellas está vacía:

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

Una cosa que hay que tener en cuenta es usar el elemento `sly` cuando no se pudo haber anotado ningún elemento existente con la instrucción de bloque. La razón es porque los elementos `sly` disuaden el valor que ofrece el lenguaje de no alterar el HTML estático al hacerlo dinámico.

Por ejemplo, si el ejemplo anterior se hubiera ajustado ya dentro de un elemento `div`, el elemento añadido `sly` sería abusivo:

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

y el elemento `div` podría haberse anotado con la condición:

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### Comentarios de HTL {#htl-comments}

El siguiente ejemplo muestra un comentario HTL en la primera línea y un comentario HTML en la segunda.

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

Los comentarios HTL son comentarios HTML con una sintaxis adicional de tipo JavaScript. El procesador ignora por completo todo el comentario HTL y todo lo que haya dentro, eliminándolo de la salida.

Sin embargo, el contenido de los comentarios HTML estándar se transmite y se evalúan las expresiones dentro del comentario.

Los comentarios HTML no pueden contener comentarios HTL y viceversa.

### Contextos especiales {#special-contexts}

Para sacar el máximo partido a HTL, es importante entender bien las consecuencias de que se basa en la sintaxis HTML.

Consulte la [sección Mostrar contexto](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) de la especificación de HTL para obtener más información.

### Nombres de elementos y atributos {#element-and-attribute-names}

Las expresiones solo se pueden colocar en texto HTML o valores de atributo, pero no dentro de nombres de elementos o nombres de atributos, o ya no sería un HTML válido. Para definir nombres de elementos de forma dinámica, se puede utilizar la instrucción `data-sly-element` en los elementos deseados y para definir nombres de atributos de forma dinámica, incluso estableciendo varios atributos a la vez, se puede utilizar la instrucción `data-sly-attribute`.

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### Contextos sin instrucciones de bloque {#contexts-without-block-statements}

Como HTL utiliza atributos de datos para definir instrucciones de bloque, no es posible definir dichas instrucciones de bloque dentro de los siguientes contextos y allí solo se pueden utilizar expresiones:

* Comentarios HTML
* Elementos de script
* Elementos de estilo

El motivo es que el contenido de estos contextos es texto y no HTML, y los elementos HTML contenidos se considerarían como datos de caracteres simples. Por lo tanto, sin elementos HTML reales, tampoco puede haber atributos `data-sly` ejecutados.

Este planteamiento puede parecer que supone una restricción significativa. Sin embargo, es preferible porque el lenguaje de plantilla de HTML solo debe generar una salida de HTML válida. La sección [API de uso para la lógica de acceso](#use-api-for-accessing-logic) que aparece a continuación presenta cómo se puede llamar a la lógica adicional desde la plantilla, que se puede utilizar si es necesaria para preparar resultados complejos para estos contextos. Para enviar datos desde el back-end a un script front-end, genere una cadena JSON con la lógica del componente y colóquela en un atributo de datos mediante una expresión HTL simple.

En el siguiente ejemplo se ilustra el comportamiento de los comentarios HTML, pero en los elementos de script o estilo, se observaría el mismo comportamiento:

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

Genera algo parecido al siguiente HTML:

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### Contextos explícitos requeridos {#explicit-contexts-required}

Como se explica en la sección [Escape automático según el contexto](#automatic-context-aware-escaping) que aparece a continuación, uno de los objetivos de HTL es reducir los riesgos de introducir vulnerabilidades de scripts entre sitios (XSS) mediante la aplicación automática de escape según el contexto a todas las expresiones. HTL detecta el contexto de las expresiones en el marcado de HTML, pero no analiza JavaScript o CSS en línea, por lo que los desarrolladores deben especificar el contexto exacto de estas expresiones.

Dado que no se aplican los resultados de escape correctos en vulnerabilidades XSS, HTL elimina la salida de todas las expresiones que están en contextos de script y estilo cuando no se ha declarado el contexto.

A continuación, se muestra un ejemplo de cómo establecer el contexto para las expresiones colocadas dentro de secuencias de comandos y estilos:

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

Para obtener más información acerca de cómo controlar el escape, consulte la sección [Contexto de muestra del lenguaje de expresión](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) de las especificaciones HTL.

## Capacidades generales de HTL {#general-capabilities-of-htl}

Esta sección muestra rápidamente las funciones generales del lenguaje de plantilla HTML.

### API de uso para acceder a la lógica {#use-api-for-accessing-logic}

Lenguaje de plantilla HTML - HTL - La API para uso de Java permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada mediante `data-sly-use`. Este proceso permite encapsular toda la lógica empresarial compleja en el código Java, mientras que el código HTL solo trata la producción de marcado directo.

Consulte el documento [API de uso de Java de HTL](java-use-api.md) para obtener más información.

### Escape automático según el contexto {#automatic-context-aware-escaping}

Consideremos el siguiente ejemplo:

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

En la mayoría de los lenguajes de plantilla, este ejemplo podría crear una vulnerabilidad de ejecución de scripts en sitios múltiples (XSS), ya que incluso cuando todas las variables se escapan de forma automática de HTML, el atributo `href` debe seguir siendo específicamente de escape de URL. Esta omisión es uno de los errores más comunes, ya que puede olvidarse fácilmente, y es difícil detectarla de manera automatizada.

Como ayuda, el lenguaje de plantilla HTML omite automáticamente cada variable según el contexto en el que se coloca. Este planteamiento es posible gracias al hecho de que HTL conoce la sintaxis de HTML.

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

El ejemplo inicial genera el siguiente resultado:

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

Vea cómo los dos atributos se han escapado de forma diferente, ya que HTL sabe que los atributos `href` y `src` deben evitarse para el contexto URI. Además, si la URI empezara por `javascript:`, el atributo se habría eliminado por completo, a menos que el contexto se cambiara explícitamente a otro.

Para obtener más información acerca de cómo controlar el escape, consulte la sección [Contexto de muestra del lenguaje de expresión](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) de las especificaciones de HTL.

### Eliminación automática de atributos vacíos {#automatic-removal-of-empty-attributes}

Consideremos el siguiente ejemplo:

```xml
<p class="${properties.class}">some text</p>
```

Si el valor de la propiedad `class` está vacío, el lenguaje de plantilla HTML quita automáticamente todo el atributo `class` de la salida.

De nuevo, este proceso es posible, ya que HTL conoce la sintaxis de HTML y, por lo tanto, puede mostrar condicionalmente atributos con valores dinámicos solo si su valor no está vacío. La razón es muy conveniente porque evita añadir un bloque de condición alrededor de los atributos, lo que habría hecho que el marcado no fuera válido ni legible.

Además, el tipo de variable colocada en la expresión es importante:

* **String:**
   * **not empty:** establece la cadena como un valor de atributo.
   * **empty:** quita el atributo por completo.

* **Number:** establece el valor como un valor de atributo.

* **Boolean:**
   * **true:** muestra el atributo sin valor (como atributo HTML booleano)
   * **false:** elimina completamente el atributo.

Este es un ejemplo de cómo una expresión booleana permitiría controlar un atributo HTML booleano:

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

Para configurar atributos, la instrucción `data-sly-attribute` también puede ser útil.

## Patrones comunes con HTL {#common-patterns-with-htl}

Esta sección presenta algunos escenarios comunes. Explica la mejor manera de resolver estos escenarios con el lenguaje de plantilla HTML.

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

En el segundo ejemplo anterior, si los elementos `head` y `body` de HTML se colocan en diferentes archivos, la plantilla `clientlib.html` debe cargarse en cada archivo que la necesite.

La sección de las instrucciones de plantilla y llamada de la [especificación de HTL](specification.md) proporciona más detalles acerca de cómo funcionan la declaración y la llamada a estas plantillas.

### Pasaje de datos al cliente {#passing-data-to-the-client}

La forma más adecuada de pasar datos al cliente en general, pero aún más con HTL, es usar atributos `data`.

En el siguiente ejemplo se muestra cómo serializar un objeto en JSON (también posible en Java) para pasarlo al cliente. A continuación, se puede colocar fácilmente en un atributo `data`:

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

A partir de ahí, es fácil imaginar cómo un JavaScript del lado del cliente puede acceder a ese atributo y volver a analizar el JSON. Este método sería el JavaScript correspondiente para colocar en una biblioteca de cliente, por ejemplo:

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### Uso de plantillas del lado del cliente {#working-with-client-side-templates}

Un caso especial, en el que la técnica explicada en la sección [Limitaciones de elevación de contextos especiales](#lifting-limitations-of-special-contexts) puede utilizarse legítimamente, es escribir plantillas del lado del cliente (como Handlebars por ejemplo) que se encuentran dentro de los elementos `scrip`. La razón por la que esta técnica se puede utilizar de forma segura en ese caso es porque el elemento `script` no contiene JavaScript como se supone, sino más elementos HTML. Aquí hay un ejemplo de cómo funcionaría:

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

El marcado del elemento `script` puede incluir instrucciones de bloque HTL sin contextos explícitos, ya que el contenido de la plantilla Handlebars está aislado en su propio archivo. Además, en este ejemplo se muestra cómo se puede combinar HTL ejecutado en el lado del servidor (como en el elemento `h2`) con un lenguaje de plantilla ejecutado en el lado del cliente, como Handlebars (mostrado en el elemento `h3`).

Sin embargo, una técnica más moderna sería utilizar el elemento HTML `template` en su lugar, ya que la ventaja sería que no es necesario aislar el contenido de las plantillas en archivos separados.

### Limitaciones de elevación de contextos especiales {#lifting-limitations-of-special-contexts}

En los casos especiales en los que es necesario evitar las restricciones de los contextos de script, estilo y comentario, es posible aislar su contenido en un archivo HTL independiente. HTL interpreta todo lo que hay en su propio archivo como un fragmento de HTML estándar, ignorando cualquier contexto limitante desde el que se incluyó.

Consulte la sección [Trabajo con plantillas del lado del cliente](#working-with-client-side-templates) más abajo para ver un ejemplo.

>[!CAUTION]
>
>Esta técnica puede introducir vulnerabilidades de ejecución de scripts en sitios múltiples (XSS). Las cuestiones de seguridad deben estudiarse cuidadosamente si se utiliza este método. Por lo general, hay mejores maneras de implementar esto que confiar en esta práctica.
