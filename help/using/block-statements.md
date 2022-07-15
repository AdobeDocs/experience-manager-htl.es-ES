---
title: ¿Qué son las declaraciones de bloque HTL?
description: Obtenga información sobre las instrucciones de bloque HTL o el lenguaje de plantilla de HTML (HTL). Las instrucciones de bloque son atributos de datos personalizados que se agregan directamente al HTML existente.
exl-id: a517dcef-ab7a-4d4c-a1a9-2e57aad034f7
source-git-commit: 79d299766da07dae001708b396b05c73cd70d4cc
workflow-type: ht
source-wordcount: '1563'
ht-degree: 100%

---

# Instrucciones de bloque de HTL {#htl-block-statements}

Las instrucciones de bloque de Lenguaje de plantilla HTML (HTL) son atributos personalizados `data` añadidos directamente al HTML existente. Esto permite realizar anotaciones sencillas y discretas en una página HTML estática prototipo, convirtiéndola en una plantilla dinámica que funcione sin dañar la validez del código HTML.

## Información general de bloque {#overview}

Los complementos de bloque HTL se definen con atributos `data-sly-*` establecidos en elementos HTML. Los elementos pueden tener una etiqueta de cierre o ser de cierre automático. Los atributos pueden tener valores (que pueden ser cadenas o expresiones estáticas) o simplemente ser atributos booleanos (sin valor).

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

Todos los atributos `data-sly-*` evaluados se eliminan del marcado generado.

### Identificadores {#identifiers}

Una instrucción de bloque también puede ir seguida de un identificador:

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

La instrucción de bloque puede utilizar el identificador de varias formas. A continuación, se muestran algunos ejemplos:

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

Los identificadores de nivel superior no distinguen entre mayúsculas y minúsculas (ya que se pueden configurar a través de atributos HTML que no distinguen entre mayúsculas y minúsculas), pero todas sus propiedades distinguen entre mayúsculas y minúsculas.

## Instrucciones de bloque disponibles {#available-block-statements}

Hay varias instrucciones de bloque disponibles. Cuando se utiliza en el mismo elemento, la siguiente lista de prioridad define cómo se evalúan las instrucciones de bloque:

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`, `data-sly-include`, `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`, `data-sly-repeat`
1. `data-sly-attribute`

Cuando dos instrucciones de bloque tienen la misma prioridad, su orden de evaluación es de izquierda a derecha.

### use {#use}

`data-sly-use` inicializa un objeto de ayuda (definido en JavaScript o Java) y lo expone a través de una variable.

Inicialice un objeto JavaScript, donde el archivo de origen se encuentra en el mismo directorio que la plantilla. Tenga en cuenta que el nombre de archivo debe usarse:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inicialice una clase Java, donde el archivo de origen se encuentra en el mismo directorio que la plantilla. Tenga en cuenta que se debe utilizar el nombre de clase, no el nombre de archivo:

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inicialice una clase Java, donde esa clase se instale como parte de un paquete OSGi. Tenga en cuenta que su nombre de clase completo debe usarse:

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Los parámetros se pueden pasar a la inicialización mediante opciones. Por lo general, esta función solo debe utilizarse con código HTL que esté dentro de un bloque `data-sly-template`:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inicialice otra plantilla HTL a la que se pueda llamar utilizando `data-sly-call`:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Para obtener más información sobre API de uso, consulte:
>
>* [API de uso de Java](use-api-java.md)
>* [API de uso de JavaScript](use-api-javascript.md)


#### data-sly-use con recursos {#data-sly-use-with-resources}

Esto permite obtener recursos directamente en HTL con `data-sly-use` y no requiere escribir código para obtenerlo.

Por ejemplo:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>Consulte también la sección [Ruta no siempre requerida.](#path-not-required)

### unwrap {#unwrap}

`data-sly-unwrap` elimina el elemento host del marcado generado al conservar su contenido. Esto permite la exclusión de elementos que son necesarios como parte de la lógica de presentación de HTL pero que no se desean en la salida real.

Sin embargo, esta instrucción debe utilizarse con moderación. En general, es mejor mantener el marcado HTL lo más cerca posible del marcado de salida deseado. En otras palabras, al agregar instrucciones de bloque HTL, intente lo más posible simplemente anotar el HTML existente, sin introducir nuevos elementos.

Por ejemplo, esto

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

producirá

```xml
<p>Hello World</p>
```

Mientras que esto

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

producirá

```xml
Hello World
```

También es posible usar unwrap condicionalmente en un elemento:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

### set {#set}

`data-sly-set` define un nuevo identificador con un valor predefinido.

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

`data-sly-text` reemplaza el contenido de su elemento host con el texto especificado.

Por ejemplo, esto

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Ambos mostrarán el valor de `jcr:description` como texto de párrafo. La ventaja del segundo método es que permite realizar anotaciones discretas en HTML manteniendo el contenido estático del marcador de posición del diseñador original.

### attribute {#attribute}

`data-sly-attribute` agrega atributos al elemento host.

Por ejemplo, esto

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Ambos establecerán el atributo `title` en el valor de `jcr:title`. La ventaja del segundo método es que permite realizar anotaciones discretas en HTML manteniendo el contenido estático del marcador de posición del diseñador original.

Los atributos se resuelven de izquierda a derecha, y la instancia más a la derecha de un atributo (literal o definida mediante `data-sly-attribute`) tiene prioridad sobre cualquier instancia del mismo atributo (definida literalmente o mediante `data-sly-attribute`) definida a su izquierda.

Tenga en cuenta que un atributo (`literal` o configurado mediante `data-sly-attribute`) cuyo valor se evalúa como la cadena vacía se eliminará en el marcado final. La única excepción a esta regla es que se conservará un atributo literal establecido en una cadena vacía literal. Por ejemplo,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produce

```xml
<div></div>
```

pero,

```xml
<div class="" data-sly-attribute.id=""></div>
```

produce

```xml
<div class=""></div>
```

Para definir varios atributos, pase un objeto map que contenga pares de clave-valor correspondientes a los atributos y sus valores. Por ejemplo, suponiendo que

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

Y luego,

```xml
<div data-sly-attribute="${attrMap}"></div>
```

produce

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

### element {#element}

`data-sly-element` reemplaza el nombre del elemento del elemento host.

Por ejemplo,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Reemplaza el `h1` con el valor `titleLevel`.

Por motivos de seguridad, `data-sly-element` solo acepta los siguientes nombres de elementos:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

Para establecer otros elementos, la seguridad XSS debe estar desactivada (`@context='unsafe'`).

### test {#test}

`data-sly-test` elimina condicionalmente el elemento host y su contenido. Un valor de `false` elimina el elemento; un valor de `true` retiene el elemento.

Por ejemplo, el elemento `p` y su contenido solo se procesarán si `isShown` es `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

El resultado de una prueba se puede asignar a una variable que se puede utilizar más adelante. Esto se utiliza generalmente para construir la lógica “if else”, ya que no hay ninguna instrucción else explícita:

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

Una vez configurada, la variable tiene un ámbito global dentro del archivo HTL.

A continuación se muestran algunos ejemplos de comparación de valores:

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div>

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

### repeat {#repeat}

Con `data-sly-repeat` puede repetir un elemento varias veces basándose en la lista especificada.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Esto funciona del mismo modo que `data-sly-list`, excepto que no necesita un elemento contenedor.

El siguiente ejemplo muestra que también puede hacer referencia al *item* para los atributos:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` repite el contenido del elemento host para cada propiedad enumerable del objeto proporcionado.

Este es un bucle simple:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Las siguientes variables predeterminadas están disponibles en el ámbito de la lista:

* `item`: el elemento actual de la iteración.
* `itemList`: objeto que contiene las siguientes propiedades:
* `index`: contador basado en cero (`0..length-1`).
* `count`: contador basado en uno (`1..length`).
* `first`: `true` si el elemento actual es el primer elemento.
* `middle`: `true` si el elemento actual no es ni el primer ni el último elemento.
* `last`: `true` si el elemento actual es el último elemento.
* `odd`: `true` si `index` es impar.
* `even`: `true` si `index` es par.

La definición de un identificador en la sentencia `data-sly-list` permite cambiar el nombre de las variables `itemList` y `item`. `item` se convertirá en `<variable>` y `itemList` se convertirá en `<variable>List`.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

También puede acceder a las propiedades de forma dinámica:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

### resource {#resource}

`data-sly-resource` incluye el resultado de procesar el recurso indicado mediante la resolución de sling y el proceso de renderización.

Un recurso sencillo incluye:

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Ruta no siempre requerida {#path-not-required}

Tenga en cuenta que el uso de una ruta con `data-sly-resource` no es necesario si ya tiene el recurso. Si ya tiene el recurso, puede utilizarlo directamente.

Por ejemplo, lo siguiente es correcto.

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

Sin embargo, lo siguiente también es perfectamente aceptable.

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

Se recomienda utilizar el recurso directamente cuando sea posible por los siguientes motivos.

* Si ya tiene el recurso, la reresolución mediante la ruta es un trabajo adicional e innecesario.
* El uso de la ruta cuando ya tiene el recurso puede producir resultados inesperados, ya que los recursos de Sling se pueden ajustar o sintetizar y no se proporcionan en la ruta dada.

#### Opciones {#resource-options}

Las opciones permiten una serie de variantes adicionales:

Manipulación de la ruta del recurso:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Añada (o reemplace) un selector:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Agregar, reemplazar o quitar varios selectores:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Agregue un selector a los existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Elimine algunos selectores de los existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Eliminar todos los selectores:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

Anula el tipo de recurso del recurso:

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

Cambia el modo WCM:

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

De forma predeterminada, las etiquetas de decoración AEM están desactivadas, la opción decorationTagName permite devolverlas y cssClassName para agregar clases a ese elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM tiene una lógica clara y sencilla para controlar las etiquetas de decoración que envuelven los elementos incluidos. Para obtener más información, consulte [Etiqueta de decoración](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/developing/full-stack/components-templates/decoration-tag.html?lang=es) en la documentación de desarrollo de componentes.

### include {#include}

`data-sly-include` reemplaza el contenido del elemento host con el marcado generado por el archivo de plantilla HTML indicado (HTL, JSP, ESP, etc.) cuando se procesa mediante el motor de plantillas correspondiente. El contexto de renderización del archivo incluido no incluirá el contexto actual de HTL (el del archivo incluido). Por consiguiente, para la inclusión de archivos HTL, el `data-sly-use` actual tendría que repetirse en el archivo incluido (en tal caso, normalmente es mejor utilizar `data-sly-template` y `data-sly-call`)

Una sencilla incluye:

```xml
<section data-sly-include="path/to/template.html"></section>
```

Los JSP se pueden incluir del mismo modo:

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

Las opciones permiten manipular la ruta del archivo:

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

También puede cambiar el modo WCM:

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

### Atributos de solicitud {#request-attributes}

En `data-sly-include` y `data-sly-resource` puede pasar `requestAttributes` para utilizarlos en el script HTL receptor.

Esto le permite pasar correctamente parámetros a scripts o componentes.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings"
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Java-code de la clase Settings, se utiliza el Mapa para pasar requestAttributes:

```xml
public class Settings extends WCMUsePojo {

  // used to pass is requestAttributes to data-sly-resource
  public Map<String, Object> settings = new HashMap<String, Object>();

  @Override
  public void activate() throws Exception {
    settings.put("layout", "flex");
  }
}
```

Por ejemplo, a través de un modelo Sling, puede consumir el valor del `requestAttributes` especificado.

En este ejemplo, el diseño se inserta a través del mapa desde la clase Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### template y call {#template-call}

Los bloques de plantilla se pueden usar como llamadas a funciones: en su declaración pueden obtener parámetros, que luego pueden pasarse al llamarlos. También permiten la recursión.

`data-sly-template` define una plantilla. HTL no genera el elemento host ni su contenido

`data-sly-call` llama a una plantilla definida con data-sly-template. El contenido de la plantilla denominada (parametrizada de forma opcional) reemplaza el contenido del elemento host de la llamada.

Defina una plantilla estática y luego llámela:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Defina una plantilla dinámica y luego llámela con parámetros:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

Las plantillas ubicadas en un archivo diferente se pueden inicializar con `data-sly-use`. Tenga en cuenta que en este caso `data-sly-use` y `data-sly-call` también podrían colocarse en el mismo elemento:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

Se admite la recursión de plantilla:

```xml
<template data-sly-template.nav="${ @ page}">
    <ul data-sly-list="${page.listChildren}">
        <li>
            <div class="title">${item.title}</div>
            <div data-sly-call="${nav @ page=item}" data-sly-unwrap></div>
        </li>
    </ul>
</template>
<div data-sly-call="${nav @ page=currentPage}" data-sly-unwrap></div>
```

## Elemento sly {#sly-element}

La etiqueta HTML `<sly>` se puede usar para eliminar el elemento actual, lo que permite mostrar solo sus elementos secundarios. Su funcionalidad es similar al elemento de bloque `data-sly-unwrap`:

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

Aunque no es una etiqueta HTML 5 válida, la etiqueta `<sly>` se puede mostrar en la salida final utilizando `data-sly-unwrap`:

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

El objetivo del elemento `<sly>` es hacer más obvio que el elemento no es salida. Si lo desea, puede seguir utilizando `data-sly-unwrap`.

Al igual que con `data-sly-unwrap`, intente minimizar el uso de esto.
