---
title: La API de uso de Java de HTL
description: La API de uso de Java de HTL permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada.
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: da2375a2390f0254dba9745d9f4970e88788e5d5
workflow-type: ht
source-wordcount: '1518'
ht-degree: 100%

---


# La API de uso de Java de HTL {#htl-java-use-api}

La API de uso de Java de HTL permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada.

## Caso práctico {#use-case}

La API de uso de Java de HTL permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada mediante `data-sly-use`. Esto permite encapsular toda la lógica empresarial compleja en el código Java, mientras que el código HTL solo trata la producción de marcado directo.

Un objeto de la API de uso de Java puede ser un POJO simple, creado por una implementación particular a través del constructor predeterminado del POJO.

Los POJO de la API de uso también pueden exponer un método público, denominado init, con la siguiente firma:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

El mapa `bindings` puede contener objetos que proporcionan contexto al script HTL que el objeto de la API de uso puede utilizar para su procesamiento.

## Un ejemplo sencillo {#a-simple-example}

Este ejemplo ilustra el empleo de la API de uso.

>[!NOTE]
>
>Está simplificado para ilustrar simplemente su utilización. En un entorno de producción, se recomienda emplear los [Modelos de Sling.](https://sling.apache.org/documentation/bundles/models.html)

Empezaremos con un componente HTL, llamado `info`, que no tiene una clase de uso. Consiste en un solo archivo, `/apps/my-example/components/info.html`

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

También agregamos contenido para este componente para renderizarlo en `/content/my-example/`:

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Cuando se accede a este contenido, se ejecuta el archivo HTL. Dentro del código HTL, utilizamos el objeto de contexto `properties` para acceder al `title` y `description` del recurso actual y mostrarlos. El archivo de salida `/content/my-example.html` será el siguiente:

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Añadir una clase de uso {#adding-a-use-class}

El componente `info` tal como está, no necesita una clase de uso para realizar su función simple. Sin embargo, hay casos en los que necesita hacer cosas que no se pueden hacer en HTL y por lo tanto necesita una clase de uso. Pero tenga en cuenta lo siguiente:

>[!NOTE]
>
>Una clase de uso solo debe usarse cuando algo no se pueda hacer solo en HTL.

Por ejemplo, supongamos que desea que el componente `info` muestre las propiedades `title` y `description` del recurso, pero todo en minúsculas. Como HTL no tiene un método para cadenas en minúsculas, necesitará una clase use. Podemos hacerlo añadiendo una clase de uso de Java y cambiando `/apps/my-example/component/info/info.html` de la siguiente manera:

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

Además, creamos `/apps/my-example/component/info/Info.java`.

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

Consulte [Javadocs de `com.adobe.cq.sightly.WCMUsePojo`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) para obtener más información.

Ahora recorremos las diferentes partes del código.

### Clase Java local frente a un paquete {#local-vs-bundle-java-class}

La clase de uso de Java puede instalarse de dos maneras:

* **Local**: en una instalación local, el archivo de origen Java se coloca junto al archivo HTL, en la misma carpeta de repositorio. La fuente se compila automáticamente bajo demanda. No es necesario compilar ni empaquetar por separado.
* **Paquete**: en una instalación de paquete, la clase Java debe compilarse e implementarse dentro de un paquete OSGi usando el mecanismo de implementación de paquete de AEM estándar (consulte la sección [Clase Java en paquete](#bundled-java-class)).

Para saber qué método utilizar, tenga en cuenta estos dos puntos:

* Se recomienda una **clase de uso local de Java** cuando la clase de uso es específica del componente en cuestión.
* Se recomienda una **clase de uso en paquete de Java** cuando el código Java implementa un servicio al que se accede desde varios componentes HTL.

Este ejemplo utiliza una instalación local.

### El paquete Java es la ruta del repositorio {#java-package-is-repository-path}

Cuando se utiliza una instalación local, el nombre del paquete de la clase use debe coincidir con el de la ubicación de la carpeta del repositorio, con los guiones de la ruta reemplazados por guiones bajos en el nombre del paquete.

En este caso, `Info.java` se encuentra en `/apps/my-example/components/info`, por lo que el paquete es `apps.my_example.components.info`:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>El uso de guiones en los nombres de los elementos del repositorio es una práctica recomendada en el desarrollo de AEM. Sin embargo, los guiones no se admiten en los nombres de paquetes Java. Por este motivo, **todos los guiones de la ruta del repositorio deben convertirse en guiones bajos en el nombre del paquete**.

### Ampliación de `WCMUsePojo` {#extending-wcmusepojo}

Aunque hay varias formas de incorporar una clase Java con HTL (consulte la sección [Alternativas a `WCMUsePojo`](#alternatives-to-wcmusepojo)), la más sencilla es ampliar la clase `WCMUsePojo`. Para nuestro ejemplo `/apps/my-example/component/info/Info.java`:

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Inicialización de la clase {#initializing-the-class}

Cuando la clase de uso se amplía desde `WCMUsePojo`, la inicialización se realiza anulando el método `activate`, en este caso en `/apps/my-example/component/info/Info.java`

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### Contexto {#context}

Normalmente, el método [activate](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) se utiliza para precalcular y almacenar (en variables de miembros) los valores necesarios en el código HTL, según el contexto actual (la solicitud y el recurso actuales, por ejemplo).

La clase `WCMUsePojo` proporciona acceso al mismo conjunto de objetos de contexto que están disponibles en un archivo HTL (consulte el documento [Objetos globales](global-objects.md)).

En una clase que extiende `WCMUsePojo`, se puede acceder a los objetos de contexto por nombre mediante

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

Como alternativa, se puede acceder directamente a los objetos de contexto utilizados con frecuencia mediante el método de conveniencia adecuado, tal y como se indica en esta tabla.

| Objeto | Método de conveniencia |
|---|---|
| [`PageManager`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/PageManager.html) | [`getPageManager()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageManager--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getCurrentPage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentPage--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getResourcePage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourcePage--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getPageProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageProperties--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getProperties--) |
| [`Designer`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [`getDesigner()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getDesigner--) |
| [`Design`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Design.html) | [`getCurrentDesign()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentDesign--) |
| [`Style`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Style.html) | [`getCurrentStyle()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentStyle--) |
| [`Component`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/components/Component.html) | [`getComponent()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getComponent--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getInheritedProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getInheritedPageProperties--) |
| [`Resource`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/Resource.html) | [`getResource()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResource--) |
| [`ResourceResolver`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [`getResourceResolver()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourceResolver--) |
| [`SlingHttpServletRequest`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [`getRequest()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getRequest--) |
| [`SlingHttpServletResponse`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [`getResponse()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResponse--) |
| [`SlingScriptHelper`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [`getSlingScriptHelper()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getSlingScriptHelper--) |

### Métodos de obtención {#getter-methods}

Una vez que la clase use se ha inicializado, se ejecuta el archivo HTL. Durante esta etapa, HTL generalmente extraerá el estado de varias variables de miembro de la clase use y las procesará para su presentación.

Para proporcionar acceso a estos valores desde el archivo HTL, debe definir métodos de obtención personalizados en la clase de uso según la siguiente convención de nomenclatura:

* Un método del formulario `getXyz` mostrará dentro del archivo HTL una propiedad de objeto llamada `xyz`.

En el archivo de ejemplo siguiente `/apps/my-example/component/info/Info.java`, los métodos `getTitle` y `getDescription` hacen que las propiedades de objeto `title` y `description` sean accesibles en el contexto del archivo HTL.

```java
...

public class Info extends WCMUsePojo {

    ...

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### Atributo data-sly-use {#data-sly-use-attribute}

El atributo `data-sly-use` se utiliza para inicializar la clase de uso dentro del código HTL. En nuestro ejemplo, el atributo `data-sly-use` declara que queremos usar la clase `Info`. Podemos usar solo el nombre local de la clase porque estamos utilizando una instalación local (después de colocar el archivo de origen Java en la misma carpeta que el archivo HTL). Si estuviéramos utilizando una instalación de paquete tendríamos que especificar el nombre de clase completo.

Observe el uso en este ejemplo `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

El identificador `info` (después del punto en `data-sly-use.info`) se utiliza dentro del archivo HTL para identificar la clase. El ámbito de este identificador es global dentro del archivo, una vez declarado. No se limita al elemento que contiene la instrucción `data-sly-use`.

Observe el uso en este ejemplo `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtención de propiedades {#getting-properties}

A continuación, el identificador `info` se utiliza para acceder a las propiedades de objeto `title` y `description` que se expusieron mediante los métodos de captador `Info.getTitle` y `Info.getDescription`.

Observe el uso en este ejemplo `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Salida {#output}

Ahora, cuando accedamos a `/content/my-example.html`, devolverá el siguiente archivo `/content/my-example.html`.

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>Este ejemplo se ha simplificado para ilustrar su uso. En un entorno de producción, se recomienda emplear los [Modelos de Sling.](https://sling.apache.org/documentation/bundles/models.html)

## Más allá de los conceptos básicos {#beyond-the-basics}

En esta sección presentaremos algunas funciones adicionales que van más allá del sencillo ejemplo anterior.

* Pasar parámetros a una clase de uso
* Clase de uso de Java en paquete

### Pasar parámetros {#passing-parameters}

Los parámetros se pueden pasar a una clase de uso tras la inicialización. Por ejemplo, podríamos hacer algo así:

Para obtener más información, consulte [Documentación del Motor de script HTL de Sling.](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects)

### Clase Java agrupada {#bundled-java-class}

Con una clase de uso del paquete, la clase debe compilarse, empaquetarse e implementarse en AEM utilizando el mecanismo de implementación del paquete OSGi estándar. A diferencia de una instalación local, la declaración del paquete de la clase de uso debe llamarse normalmente como en este ejemplo `/apps/my-example/component/info/Info.java`.

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

Y la instrucción `data-sly-use` debe hacer referencia al nombre de clase completo, en lugar de solo al local, como en este ejemplo `/apps/my-example/component/info/info.html`.

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
