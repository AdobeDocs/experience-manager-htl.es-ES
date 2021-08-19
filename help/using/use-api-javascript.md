---
title: HTL JavaScript Use-API
description: 'El lenguaje de plantilla HTML - HTL: La API para uso de JavaScript permite que un archivo HTL acceda al código de ayuda escrito en JavaScript.'
exl-id: e98bfbd5-fa64-48c7-bd14-477d4c5e1788
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '324'
ht-degree: 100%

---

# HTL JavaScript Use-API {#htl-javascript-use-api}

La API de uso de JavaScript de lenguaje de plantilla HTML (HTL) permite que un archivo HTL acceda al código de ayuda escrito en JavaScript. Esto permite encapsular toda la lógica empresarial compleja en el código JavaScript, mientras que el código HTL solo trata la producción de marcado directo.

Se utilizan las siguientes convenciones.

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## Un ejemplo sencillo {#a-simple-example}

Definimos un componente, `info`, ubicado en

`/apps/my-example/components/info`

Contiene dos archivos:

* **`info.js`**: un archivo JavaScript que define la clase de uso.
* **`info.html`**: un archivo HTL que define el componente `info`. Este código utilizará la funcionalidad de `info.js` a través de la API de uso.

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

También creamos un nodo de contenido que utiliza el componente `info` en

`/content/my-example`, con propiedades:

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

Esta es la estructura del repositorio resultante:

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

Tenga en cuenta la siguiente plantilla de componente:

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

La lógica correspondiente se puede escribir utilizando el siguiente JavaScript del lado del servidor, ubicado en un archivo `component.js` justo al lado de la plantilla:

```javascript
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

Esto intenta tomar el `title` de diferentes fuentes y recorta la descripción a 50 caracteres.

## Dependencias {#dependencies}

Imaginemos que tenemos una clase de utilidad que ya está equipada con funciones inteligentes, como la lógica predeterminada para el título de navegación o cortando una cadena a una longitud determinada:

```javascript
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

El patrón de dependencia también se puede utilizar para ampliar la lógica de otro componente (que suele ser `sling:resourceSuperType` del componente actual).

Imagine que el componente principal ya proporciona el `title` y que también queremos agregar un `description`:

```javascript
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

En el caso de las instrucciones `data-sly-template` que pueden ser independientes de los componentes, puede resultar útil pasar parámetros a la API de uso asociada.

Por lo tanto, en nuestro componente vamos a llamar a una plantilla que se encuentra en un archivo diferente:

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

A continuación, esta es la plantilla ubicada en `template.html`:

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

La lógica correspondiente se puede escribir utilizando el siguiente JavaScript del lado del servidor, ubicado en un archivo `template.js` justo al lado del archivo de plantilla:

```javascript
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

Los parámetros pasados se establecen en la palabra clave `this`.
