---
title: Declaraciones de bloque HTL
seo-title: Declaraciones de bloque HTL
description: Las declaraciones de bloques del lenguaje de plantilla HTML (HTL) son
  atributos de datos personalizados que se agregan directamente a HTML existente.
seo-description: Las declaraciones de bloques del lenguaje de plantilla HTML (HTL)
  son atributos de datos personalizados que se agregan directamente a HTML existente.
uuid: 0624 fb 6 e -6989-431 b-aabc -1138325393 f 1
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 58 aa 6 ea 8-1 d 45-4 f 6 f-a 77 e -4819 f 593 a 19 d
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 7a94b0b010461b29d2b74c9c717e3b218d0ca5a8

---


# Declaraciones de bloque HTL {#htl-block-statements}

Las declaraciones de bloques del lenguaje de plantilla HTML (HTL) son `data` atributos personalizados agregados directamente a HTML existente. Esto permite anotar fácilmente y sin confusión una página HTML estática prototipo, convirtiéndola en una plantilla dinámica que funcione sin interrumpir la validez del código HTML.

## elemento oscuro {#sly-element}

The **& amp; lt; giant & amp; gt; no se** muestra en el HTML resultante y se puede utilizar en lugar de la opción de desajuste de datos. El objetivo de la & amp; lt; giant & amp; gt; es para hacer que sea más evidente que el elemento no se incluye. Si desea seguir utilizando los datos totalmente desajustados.

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

Al igual que con los datos desajustados, intente minimizar su uso.

## use {#use}

**`data-sly-use`**: Inicializa un objeto de ayuda (definido en JavaScript o Java) y lo expone a través de una variable.

Inicialice un objeto JavaScript, donde el archivo de origen se encuentra en el mismo directorio que la plantilla. Tenga en cuenta que debe utilizarse el nombre de archivo:

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Inicialice una clase Java, donde el archivo de origen se encuentra en el mismo directorio que la plantilla. Tenga en cuenta que debe utilizarse el nombre de clase, no el nombre del archivo:

```xml
        <div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Inicialice una clase Java, donde dicha clase se instalará como parte de un paquete osgi. Tenga en cuenta que debe utilizarse su nombre de clase completo:

```xml
        <div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

Los parámetros se pueden pasar a la inicialización mediante *opciones*. Generalmente, esta función solo debe ser utilizada por código HTL que se encuentre dentro `data-sly-template` de un bloque:

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

Inicialice otra plantilla HTL que luego se pueda llamar mediante **`data-sly-call`**:

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Para obtener más información sobre la API de uso, consulte:
>
>* [Java Use-API](use-api-java.md)
>* [API de uso JavaScript](use-api-javascript.md)
>



## desajustar {#unwrap}

**`data-sly-unwrap`**: Quita el elemento host del marcado generado al mismo tiempo que conserva su contenido. Esto permite excluir elementos necesarios como parte de la lógica de presentación HTL pero no se desean en la salida real.

Sin embargo, esta instrucción debe usarse con moderación. En general, es mejor mantener el marcado HTL lo más cerca posible del marcado de salida previsto. En otras palabras, al agregar declaraciones de bloques HTL, intente simplemente realizar anotaciones en el HTML existente sin introducir nuevos elementos.

Por ejemplo, esto

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

producirá

```xml
<p>Hello World</p>
```

Aunque esto,

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

producirá

```xml
Hello World
```

También es posible desajustar condicionalmente un elemento:

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

## texto {#text}

**`data-sly-text`**: Reemplaza el contenido de su elemento host por el texto especificado.

Por ejemplo, esto

```xml
<p>${properties.jcr:description}</p>
```

equivale a

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

Ambos mostrarán el valor como **`jcr:description`** texto de párrafo. La ventaja del segundo método es que permite la anotación sin confusión de HTML manteniendo el contenido de marcador de posición estático del diseñador original.

## attribute {#attribute}

**data-dark-attribute**: Agrega atributos al elemento host.

Por ejemplo, esto

```xml
<div title="${properties.jcr:title}"></div>
```

equivale a

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

Ambos configuran el `title` atributo al valor de **`jcr:title`**. La ventaja del segundo método es que permite la anotación sin confusión de HTML manteniendo el contenido de marcador de posición estático del diseñador original.

Los atributos se resuelven de izquierda a derecha, con la instancia situada más a la derecha de un atributo (literal o definido mediante **`data-sly-attribute`**) teniendo prioridad sobre cualquier instancia del mismo atributo (definida literalmente o mediante **`data-sly-attribute`**) definida a su izquierda.

Tenga en cuenta que un atributo (o **`literal`** se establece mediante **`data-sly-attribute`**) cuyo valor *se evalúa* como la cadena vacía se eliminará en la marca final. La única excepción a esta regla es que se conservará un *atributo literal* definido en una *cadena* vacía de literal. Por ejemplo,

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

Para definir varios atributos, pase un objeto de mapa manteniendo pares clave-valor correspondientes a los atributos y sus valores. Por ejemplo, suponiendo,

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

**`data-sly-element`**: Reemplaza el nombre del elemento del elemento host.

Por ejemplo,

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

Reemplaza el **`h1`** valor de **`titleLevel`**.

Por razones de seguridad `data-sly-element` , acepta sólo los nombres de elementos siguientes:

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

Para definir otros elementos, es necesario desactivar la seguridad XSS ( `@context='unsafe'`).

## prueba {#test}

**`data-sly-test`:** Elimina de forma condicional el elemento de host y su contenido. Un valor `false` de quita el elemento; el valor `true` retenido el elemento.

Por ejemplo, el `p` elemento y su contenido solo se procesarán si `isShown``true`es:

```xml
<p data-sly-test="${isShown}">text</p>
```

El resultado de una prueba puede asignarse a una variable que pueda utilizarse posteriormente. Esto generalmente se utiliza para construir lógica "if else", ya que no hay ninguna otra instrucción explícita:

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

## repetir {#repeat}

Con datos de repetición, *puede repetir* un elemento varias veces en función de la lista especificada.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

Funciona de la misma forma que con data-list, excepto que no necesita un elemento contenedor.

El siguiente ejemplo muestra que también se puede hacer referencia al *elemento* para atributos:

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**: Repite el contenido del elemento host para cada propiedad enumerable del objeto proporcionado.

Este es un bucle simple:

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

Las siguientes variables predeterminadas están disponibles dentro del ámbito de la lista:

**`item`**: Elemento actual de la iteración.

**`itemList`**: Objeto que contiene las siguientes propiedades:

**`index`**: counter-based counter ( `0..length-1`).

**`count`**: contador basado en uno ( `1..length`).

`first`: `true` si el elemento actual es el primer elemento.

**`middle`**: `true` si el elemento actual no es ni el primero ni el último.

**`last`**: `true` si el elemento actual es el último elemento.

**`odd`**: `true` if `index` is odd.

**`even`**: `true` if `index` is even.

La definición de un identificador en `data-sly-list` la sentencia permite cambiar el nombre de las variables **`itemList`** y `item` las variables. **`item`** pasará a ser*** `<variable>`*** y **`itemList`** se convertirá **`*<variable>*List`**en.

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

También puede acceder dinámicamente a propiedades:

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

## medio {#resource}

**`data-sly-resource`**: Incluye el resultado de procesar el recurso indicado mediante la resolución de sling y el procesamiento.

Un recurso simple incluye:

```xml
<article data-sly-resource="path/to/resource"></article>
```

Las opciones permiten una cantidad de variantes adicionales:

Manipulación de la ruta del recurso:

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

Agregue (o sustituya) un selector:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

Agregue, sustituya o elimine varios selectores:

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

Agregue un selector a las existentes:

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

Elimine algunos selectores de las existentes:

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

De forma predeterminada, las etiquetas decoration de AEM están desactivadas, la opción decorationtagname permite devolverlos y cssclassname para agregar clases a dicho elemento.

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM ofrece una lógica clara y simple que controla las etiquetas decorativas que ajustan los elementos incluidos. Para obtener más información, consulte [Etiqueta](https://helpx.adobe.com/experience-manager/6-4/sites/developing/using/decoration-tag.html) decorativa en la documentación de componentes de desarrollo.

## include {#include}

**`data-sly-include`**: Sustituye el contenido del elemento host por el marcado generado por el archivo de plantilla HTML indicado (HTL, JSP, ESP, etc.) cuando se procesa por su correspondiente motor de plantilla. El contexto de procesamiento *del archivo* incluido no incluirá el contexto HTL actual (el del archivo *incluido*); Por lo tanto, para incluir archivos HTL, es necesario repetir la actual **`data-sly-use`** en el archivo incluido (en este caso, normalmente es mejor utilizar **`data-sly-template`** y `data-sly-call`)

Una simple inclusión:

```xml
<section data-sly-include="path/to/template.html"></section>
```

Los JSP se pueden incluir de la misma manera:

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

## template & call {#template-call}

`data-sly-template`: Define una plantilla. El elemento host y su contenido no son generados por HTL

`data-sly-call`: Llama a una plantilla definida con data-template. El contenido de la plantilla denominada (opcionalmente parametrizada) sustituye al contenido del elemento host de la llamada.

Defina una plantilla estática y llámela:

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

Defina una plantilla dinámica y llámela con parámetros:

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

Las plantillas ubicadas en un archivo diferente se pueden inicializar `data-sly-use`con. Tenga en cuenta que, en este caso `data-sly-use` , `data-sly-call` también se puede colocar en el mismo elemento:

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

Se admite la repetición de plantilla:

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

## objetos i 18 n y configuración regional {#i-n-and-locale-objects}

Cuando utilice i 18 n y HTL, ahora también puede pasar objetos de configuración regional personalizados.

```xml
${'Hello World' @ i18n, locale=request.locale}
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

Agrega la extensión html y un fragmento (# value) a una ruta.

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## Funciones HTL admitidas en AEM 6.3 {#htl-features-supported-in-aem}

Adobe Experience Manager (AEM) 6.3 admite las siguientes funciones nuevas de HTL:

### Número/formato de fecha {#number-date-formatting}

AEM 6.3 admite el formato nativo de números y fechas, sin necesidad de escribir código personalizado. Esto también admite la zona horaria y la configuración regional.

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
>Para obtener detalles completos sobre el formato que puede utilizar, consulte [especificación HTL](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md).

### uso de datos de forma rigurosa con recursos {#data-sly-use-with-resources}

Esto permite obtener recursos directamente en HTL con datos de uso absoluto y no requiere escribir código para obtener el recurso.

Por ejemplo:

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### Atributos de solicitud {#request-attributes}

Ahora, en *los recursos de inclusión oscura* y *de datos,* puede pasar *requestattributes* para usarlos en la secuencia de comandos HTL-.

Esto permite pasar parámetros correctamente a secuencias de comandos o componentes.

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings" 
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Código Java de la clase Settings, el mapa se utiliza para pasar los parámetros requestattributes:

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

Por ejemplo, a través de un modelo Sling, puede consumir el valor de los requestattributes especificados.

En este ejemplo, *el diseño* se inserta mediante Mapa de la clase Use:

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### Corrección para @ extension {#fix-for-extension}

La extensión @ funciona en todos los escenarios de AEM 6.3, antes de que pueda tener un resultado como *www.adobe.com.ht ml* y también compruebe si se agregará o no la extensión.

```xml
${ link @ extension = 'html' }
```
