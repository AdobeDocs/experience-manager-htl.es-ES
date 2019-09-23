---
title: Declaraciones de bloque HTL
seo-title: Declaraciones de bloque HTL
description: Las sentencias de bloque de lenguaje de plantilla HTML (HTL) son atributos de datos personalizados que se agregan directamente al HTML existente.
seo-description: 'Las sentencias de bloque de lenguaje de plantilla HTML (HTL) son atributos de datos personalizados que se agregan directamente al HTML existente. '
uuid: 0624fb6e-6989-431b-aabc-1138325393f1
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: afc29cbad83caeb549097da3fc33fd9147f1157a

---


# Declaraciones de bloque HTL {#htl-block-statements}

Las sentencias de bloque de lenguaje de plantilla HTML (HTL) son atributos personalizados que se agregan directamente `data` al HTML existente. Esto permite realizar anotaciones sencillas y discretas de una página HTML estática prototipo, convirtiéndola en una plantilla dinámica que funcione sin romper la validez del código HTML.

## element {#sly-element}

El elemento **** &lt;asto&gt; no se muestra en el HTML resultante y se puede utilizar en lugar del desajuste de datos. El objetivo del elemento &lt;push&gt; es hacer más obvio que no se genera el elemento. Si lo desea, puede seguir utilizando data-hidden-unwrap.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Al igual que con el desajuste de datos, trate de minimizar el uso de esto.

## use {#use}

**`data-sly-use`**:: Inicializa un objeto auxiliar (definido en JavaScript o Java) y lo expone a través de una variable.

Inicialice un objeto JavaScript, donde el archivo de origen se encuentra en el mismo directorio que la plantilla. Tenga en cuenta que se debe utilizar el nombre del archivo:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inicialice una clase Java, donde el archivo de origen se encuentra en el mismo directorio que la plantilla. Tenga en cuenta que se debe utilizar el nombre de clase, no el nombre del archivo:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inicialice una clase Java, donde dicha clase se instala como parte de un paquete OSGi. Tenga en cuenta que se debe utilizar su nombre de clase completo:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Los parámetros se pueden pasar a la inicialización mediante *opciones*. Por lo general, esta función solo debe ser utilizada por el código HTML que se encuentra dentro de un `data-sly-template` bloque:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inicialice otra plantilla HTL a la que se pueda llamar mediante **`data-sly-call`**:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Para obtener más información sobre Use-API, consulte:
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)
>



## unwrap {#unwrap}

**`data-sly-unwrap`**: Removes the host element from the generated markup while retaining its content. This allows the exclusion of elements that are required as part of HTL presentation logic but are not desired in the actual output.

However, this statement should be used sparingly. In general it is better to keep the HTL markup as close as possible to the intended output markup. In other words, when adding HTL block statements, try as much as possible to simply annotate the existing HTML, without introducing new elements.

For example, this

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

will produce

```xml
<p>Hello World</p>
```

Whereas this,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

will produce

```xml
Hello World
```

It is also possible to conditionally unwrap an element:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## texto {#text}

**`data-sly-text`**: Replaces the content of its host element with the specified text.

For example, this

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Ambos mostrarán el valor de **`jcr:description`** como texto de párrafo. The advantage of the second method is that is allows the unobtrusive annotation of HTML while keeping the static placeholder content from the original designer.

## attribute {#attribute}

**data-entir-atributo**: Agrega atributos al elemento host.

Por ejemplo:

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Ambos establecerán el `title` atributo en el valor de **`jcr:title`**. La ventaja del segundo método es que permite realizar anotaciones discretas de HTML manteniendo el contenido estático del marcador de posición del diseñador original.

Los atributos se resuelven de izquierda a derecha, y la instancia más a la derecha de un atributo (ya sea literal o definida mediante **`data-sly-attribute`**) tiene prioridad sobre cualquier instancia del mismo atributo (definida literalmente o mediante **`data-sly-attribute`**) definida a la izquierda.

Tenga en cuenta que un atributo (ya sea **`literal`** o definido mediante **`data-sly-attribute`**) cuyo valor *se evalúa* en la cadena vacía se eliminará en el marcado final. La única excepción a esta regla es que se conservará un atributo *literal* establecido en una cadena *literal* vacía. Por ejemplo,

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

produce,

```xml
<div></div>
```

pero,

```xml
<div class="" data-sly-attribute.id=""></div>
```

produce,

```xml
<div class=""></div>
```

Para definir varios atributos, pase un objeto de mapa con pares clave-valor correspondientes a los atributos y sus valores. Por ejemplo, suponiendo que

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

produce,

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

## elemento {#element}

**`data-sly-element`**:: Reemplaza el nombre del elemento del elemento host.

Por ejemplo,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Reemplaza el **`h1`** por el valor de **`titleLevel`**.

Por motivos de seguridad, `data-sly-element` acepta solamente los siguientes nombres de elementos:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Para establecer otros elementos, la seguridad XSS debe estar desactivada ( `@context='unsafe'`).

## prueba {#test}

**`data-sly-test`** :: Elimina condicionalmente el elemento host y su contenido. Un valor de `false` elimina el elemento; un valor de `true` conserva el elemento.

Por ejemplo, el `p` elemento y su contenido solo se procesarán si `isShown` es `true`:

```xml
<p data-sly-test="${isShown}">text</p>
```

El resultado de una prueba se puede asignar a una variable que se puede utilizar más adelante. Esto generalmente se utiliza para construir la lógica "if else", ya que no hay ninguna otra afirmación explícita:

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

## repeat {#repeat}

Con la repetición de datos puede *repetir* un elemento varias veces basándose en la lista especificada.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Funciona de la misma manera que la lista de datos, excepto que no necesita un elemento contenedor.

El siguiente ejemplo muestra que también puede hacer referencia al *elemento* para atributos:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**:: Repite el contenido del elemento host para cada propiedad enumerable del objeto proporcionado.

Here is a simple loop:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Las siguientes variables predeterminadas están disponibles en el ámbito de la lista:

**`item`**:: The current item in the iteration.

**`itemList`**:: Object holding the following properties:

**`index`**:: zero-based counter ( ).`0..length-1`

**`count`**:: one-based counter ( ).`1..length`

`first`::  if the current item is the first item.`true`

**`middle`**::  if the current item is neither the first nor the last item.`true`

**`last`**::  if the current item is the last item.`true`

**`odd`**::  if  is odd.`true``index`

**`even`**::  if  is even.`true``index`

Defining an identifier on the  statement allows you to rename the  and  variables. `data-sly-list`**`itemList`**`item` **`item`** will become *** *** and  will become .`<variable>`**`itemList`****`*<variable>*List`**

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

## medio {#resource}

**`data-sly-resource`**:: Includes the result of rendering the indicated resource through the sling resolution and rendering process.

A simple resource include:

```xml
<article data-sly-resource="path/to/resource"></article>
```

Las opciones permiten una serie de variantes adicionales:

Manipulating the path of the resource:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Agregue (o reemplace) un selector:

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

Quitar algunos selectores de los existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

Quitar todos los selectores:

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

De forma predeterminada, las etiquetas de decoración de AEM están desactivadas, la opción DecorationTagName permite devolverlas y cssClassName para agregar clases a ese elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM ofrece una lógica clara y sencilla que controla las etiquetas de decoración que envuelven los elementos incluidos. Para obtener más información, consulte [Decoration Tag](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) en la documentación de desarrollo de componentes.

## include {#include}

**`data-sly-include`**:: Reemplaza el contenido del elemento host con el marcado generado por el archivo de plantilla HTML indicado (HTL, JSP, ESP, etc.) cuando se procesa con su motor de plantilla correspondiente. El contexto de procesamiento del archivo ** incluido no incluirá el contexto HTL actual (el del archivo ** incluido); En consecuencia, para incluir los archivos HTL, el archivo actual **`data-sly-use`** tendría que repetirse en el archivo incluido (en tal caso, normalmente es mejor utilizar **`data-sly-template`** y `data-sly-call`)

Un sencillo ejemplo:

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

## plantilla y llamada {#template-call}

`data-sly-template`:: Define una plantilla. HTL no genera el elemento host ni su contenido

`data-sly-call`:: Llama a una plantilla definida con una plantilla data-astnito-template. El contenido de la plantilla llamada (parametrizada de forma opcional) reemplaza al contenido del elemento host de la llamada.

Defina una plantilla estática y, a continuación, llámala:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Defina una plantilla dinámica y, a continuación, llámala con parámetros:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

Las plantillas ubicadas en un archivo diferente se pueden inicializar con `data-sly-use`. Note that in this case  and  could also be placed on the same element:`data-sly-use``data-sly-call`

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

Template recursion is supported:

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

## Objetos i18n y de configuración regional {#i-n-and-locale-objects}

When you are using i18n and HTL, you can now also pass in custom locale objects.

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## URL manipulation {#url-manipulation}

A new set of url manipulations is available.

See the following examples on their usage:

Adds the html extension to a path.

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

Adds the html extension and a selector to a path.

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

Adds the html extension and a fragment (#value) to a path.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## HTL Features Supported in AEM 6.3 {#htl-features-supported-in-aem}

The following new HTL features are supported in Adobe Experience Manager (AEM) 6.3:

### Number/Date-formatting {#number-date-formatting}

AEM 6.3 supports native formatting of numbers and dates, without writing custom code. This also supports timezone and locale.

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
>Para obtener más información sobre el formato que puede utilizar, consulte la especificación [HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### uso de datos con recursos {#data-sly-use-with-resources}

Esto permite obtener recursos directamente en HTL con uso de datos de forma remota y no requiere escribir código para obtener el recurso.

Por ejemplo:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Request-attributes {#request-attributes}

En el recurso ** data-hidden-include *y* data-hidden ahora puede pasar *requestAttributes* para utilizarlos en el script HTL receptor.

Esto le permite pasar correctamente parámetros a scripts o componentes.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Java-code de la clase Settings, el mapa se utiliza para pasar requestAttributes:

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

Por ejemplo, mediante un modelo Sling, puede consumir el valor de requestAttributes especificado.

En este ejemplo, *layout* se inserta mediante el mapa desde la clase Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Corrección para @extension {#fix-for-extension}

La extensión @funciona en todos los escenarios de AEM 6.3, antes de que se pueda obtener un resultado como *www.adobe.com.html* y también comprueba si se debe añadir o no la extensión.

```xml
${ link @ extension = 'html' }
```
