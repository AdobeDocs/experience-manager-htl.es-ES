---
title: HTML JavaScript Use-API
seo-title: HTML JavaScript Use-API
description: HTML Template Language - HTL - JavaScript Use-API permite que un archivo HTL acceda al código auxiliar escrito en JavaScript.
seo-description: HTML Template Language - HTL - JavaScript Use-API permite que un archivo HTL acceda al código auxiliar escrito en JavaScript.
uuid: 7ab34b10-30ac-44d6-926b-0234f52e5541
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 18871af8-e44b-4eec-a483-fcc765dae58f
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: bd1962e25d152be4f1608d0a83d8d5b3e728b4aa

---


# HTML JavaScript Use-API {#htl-javascript-use-api}

La API de uso de JavaScript de HTML Template Language (HTL) permite que un archivo HTL acceda al código auxiliar escrito en JavaScript. Esto permite encapsular toda la lógica empresarial compleja en el código JavaScript, mientras que el código HTL solo se ocupa de la producción directa de marcado.

## Un ejemplo sencillo {#a-simple-example}

Definimos un componente, `info`, ubicado en

**`/apps/my-example/components/info`**

Contiene dos archivos:

* **`info.js`**:: un archivo JavaScript que define la clase use.
* `info.html`:: un archivo HTL que define el componente `info`. Este código utilizará la funcionalidad de `info.js` mediante use-API.

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

También se crea un nodo de contenido que utiliza el **`info`** componente en

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

Considere la siguiente plantilla de componente:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

La lógica correspondiente se puede escribir con el siguiente JavaScript del lado del ***servidor*** , ubicado en un `component.js` archivo junto a la plantilla:

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

De este modo, se intenta llevar el `title` contenido de diferentes fuentes y recortar la descripción a 50 caracteres.

## Dependencias {#dependencies}

Imaginemos que tenemos una clase de utilidades que ya está equipada con características inteligentes, como la lógica predeterminada para el título de navegación o cortando una cadena a una cierta longitud:

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

El patrón de dependencia también se puede utilizar para ampliar la lógica de otro componente (que normalmente es el `sling:resourceSuperType` del componente actual).

Imagine que el componente principal ya proporciona el `title`y que también queremos agregar un **`description`** :

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

En el caso de **`data-sly-template`** afirmaciones que pueden ser independientes de los componentes, puede resultar útil pasar parámetros a la Use-API asociada.

En nuestro componente vamos a llamar a una plantilla que se encuentra en un archivo diferente:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

Esta es la plantilla ubicada en `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

La lógica correspondiente se puede escribir con el siguiente JavaScript del lado del ***servidor*** , ubicado en un `template.js` archivo junto al archivo de plantilla:

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
