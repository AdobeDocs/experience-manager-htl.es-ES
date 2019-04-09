---
title: HTL JavaScript Use-API
seo-title: HTL JavaScript Use-API
description: La plantilla HTML Langugae - HTL - API de uso JavaScript permite a un
  archivo HTL acceder al código de ayuda escrito en JavaScript.
seo-description: La plantilla HTML Langugae - HTL - API de uso JavaScript permite
  a un archivo HTL acceder al código de ayuda escrito en JavaScript.
uuid: 7 ab 34 b 10-30 ac -44 d 6-926 b -0234 f 52 e 5541
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 18871 af 8-e 44 b -4 eec-a 483-fcc 765 dae 58 f
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL JavaScript Use-API {#htl-javascript-use-api}

La API de uso JavaScript de plantilla HTML (HTL) javascript permite a un archivo HTL acceder al código de ayuda escrito en JavaScript. Esto permite encapsular toda la lógica comercial compleja en el código JavaScript, mientras que el código HTL trata únicamente la producción directa de marcas.

## Ejemplo sencillo {#a-simple-example}

Definimos un `info`componente, ubicado en

**`/apps/my-example/components/info`**

Contiene dos archivos:

* **`info.js`**: un archivo JavaScript que define la clase de uso.
* `info.html`: un archivo HTL que define el componente `info`. Este código utilizará la funcionalidad de `info.js` la API de uso.

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};    
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];    
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

También creamos un nodo de contenido que utiliza **`info`** el componente en

**`/content/my-example`**, con propiedades:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Esta es la estructura de repositorio resultante:

### Estructura del repositorio {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          }, 
          "info.js": {
            ...
          }
        }
      }
    }
 },     
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

Considere la plantilla de componente siguiente:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

La lógica correspondiente se puede escribir utilizando JavaScript en ***el lado*** del servidor siguiente, ubicado en `component.js` un archivo junto a la plantilla:

```
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };
 
    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);
 
    return {
        title: title,
        description: description
    };
});
```

Esto intenta tomar el `title` de diferentes fuentes y recortar la descripción a 50 caracteres.

## Dependencias {#dependencies}

Imagine que tenemos una clase de utilidades que ya está equipada con funciones inteligentes, como la lógica predeterminada para el título de navegación o que cortan una cadena en una longitud determinada:

```
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };
 
    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);
 
    return {
        title: title,
        description: description
    };
});
```

## Ampliación {#extending}

El patrón de dependencia también puede utilizarse para ampliar la lógica de otro componente (que suele ser `sling:resourceSuperType` el del componente actual).

Imagine que el componente principal ya proporciona la `title`y también queremos agregar **`description`** a:

```
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };
 
    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);
 
    return component;
});
```

## Pasar parámetros a una plantilla {#passing-parameters-to-a-template}

En el caso de **`data-sly-template`** las declaraciones que pueden ser independientes de los componentes, puede resultar útil pasar parámetros a la API de uso asociada.

Así, en nuestro componente vamos a llamar a una plantilla ubicada en otro archivo:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Ésta es la plantilla ubicada en `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

La lógica correspondiente se puede escribir utilizando JavaScript en ***el lado*** del servidor siguiente, ubicado en `template.js` un archivo junto al archivo de plantilla:

```
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };
 
    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);
 
    return {
        title: title,
        description: description
    };
});
```

Los parámetros pasados se establecen en la `this` palabra clave.
