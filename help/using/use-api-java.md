---
title: HTL Java Use-API
description: 'Lenguaje de plantilla HTML - HTL - La API para uso de Java permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada. '
translation-type: tm+mt
source-git-commit: ee712ef61018b5e05ea052484e2a9a6b12e6c5c8
workflow-type: tm+mt
source-wordcount: '2558'
ht-degree: 1%

---


# HTL Java Use-API {#htl-java-use-api}

El lenguaje de plantilla HTML (HTL) Java Use-API permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada mediante `data-sly-use`. Esto permite encapsular toda la lógica empresarial compleja en el código Java, mientras que el código HTL sólo se ocupa de la producción directa de marcado.

Un objeto Use-API de Java puede ser un POJO simple, creado por una implementación concreta a través del constructor predeterminado del POJO.

Los POJO de Use-API también pueden exponer un método público, denominado init, con la siguiente firma:

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

El mapa `bindings` puede contener objetos que proporcionan contexto a la secuencia de comandos HTML ejecutada actualmente que el objeto Use-API puede utilizar para su procesamiento.

## Un Ejemplo Simple {#a-simple-example}

Inicios con un componente HTL que no tenga una clase de uso. Consiste en un solo archivo, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

También agregamos contenido para este componente que se procesará en `/content/my-example/`:

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Cuando se accede a este contenido, se ejecuta el archivo HTL. Dentro del código HTML utilizamos el objeto de contexto `properties` para acceder a los `title` y `description` del recurso actual y mostrarlos. El HTML de salida será:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Añadir una clase de uso {#adding-a-use-class}

El componente **info** tal como está no necesita una clase use para realizar su función (muy simple). Sin embargo, hay casos en los que se necesita hacer cosas que no se pueden hacer en HTL y por lo tanto se necesita una clase de uso. Pero tenga en cuenta lo siguiente:

>[!NOTE]
>
>Una clase de uso sólo debe usarse cuando no se pueda hacer algo solo en HTL.

Por ejemplo, supongamos que desea que el componente `info` muestre las propiedades `title` y `description` del recurso, pero todo en minúsculas. Dado que HTL no tiene un método para quitar cadenas, necesitará una clase use. Podemos hacerlo agregando una clase de uso de Java y cambiando el `info.html` de la siguiente manera:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

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

En las siguientes secciones, pasamos por las diferentes partes del código.

### Clase Java local vs. paquete {#local-vs-bundle-java-class}

La clase de uso de Java se puede instalar de dos maneras: **local** o **paquete**. En este ejemplo se utiliza una instalación local.

En una instalación local, el archivo de origen Java se coloca junto al archivo HTL, en la misma carpeta de repositorio. La fuente se compila automáticamente a petición. No se requiere una compilación o un paso de embalaje por separado.

En una instalación de paquete, la clase Java debe compilarse e implementarse dentro de un paquete OSGi usando el mecanismo de implementación de paquete de AEM estándar (consulte [Clase Java agrupada](#bundled-java-class)).

>[!NOTE]
>
>Se recomienda una **clase de uso local de Java** cuando la clase de uso es específica del componente en cuestión.
>
>Se recomienda una **clase use-class** de Java paquete cuando el código Java implemente un servicio al que se accede desde múltiples componentes HTL.

### El paquete Java es la ruta del repositorio {#java-package-is-repository-path}

Cuando se utiliza una instalación local, el nombre del paquete de la clase use debe coincidir con el de la ubicación de la carpeta del repositorio, reemplazando cualquier guión de la ruta por guiones bajos en el nombre del paquete.

En este caso `Info.java` se encuentra en `/apps/my-example/components/info`, por lo que el paquete es `apps.my_example.components.info`:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>El uso de guiones en los nombres de los elementos del repositorio es una práctica recomendada en AEM desarrollo. Sin embargo, los guiones son ilegales en los nombres de paquetes de Java. Por este motivo, **todos los guiones de la ruta del repositorio deben convertirse en caracteres de subrayado en el nombre del paquete**.

### Extendiendo `WCMUsePojo` {#extending-wcmusepojo}

Aunque hay varias formas de incorporar una clase Java con HTL (consulte Alternativas a `WCMUsePojo`), la más simple es ampliar la clase `WCMUsePojo`:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### Inicializando la clase {#initializing-the-class}

Cuando la clase use se amplía desde `WCMUsePojo`, la inicialización se realiza anulando el método `activate`:

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

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

Normalmente, el método [activate](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) se utiliza para precalcular y almacenar (en variables de miembros) los valores necesarios en el código HTML, según el contexto actual (la solicitud y el recurso actuales, por ejemplo).

La clase `WCMUsePojo` proporciona acceso al mismo conjunto de objetos de contexto que están disponibles en un archivo HTL (consulte [Objetos globales](global-objects.md)).

En una clase que se extiende `WCMUsePojo`, se puede acceder a los objetos de contexto por nombre mediante

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

También se puede acceder directamente a los objetos de contexto más utilizados mediante el método **de conveniencia** correspondiente:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Diseñador](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Diseño](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Estilo](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Medio](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Obtener métodos {#getter-methods}

Una vez inicializada la clase use, se ejecuta el archivo HTL. Durante esta etapa, HTL generalmente extrae el estado de varias variables de miembros de la clase use y las procesa para su presentación.

Para proporcionar acceso a estos valores desde el archivo HTML, debe definir métodos de captador personalizados en la clase use según la siguiente convención de nomenclatura:

* Un método del formulario `getXyz` expondrá dentro del archivo HTML una propiedad de objeto llamada `xyz`.

En el ejemplo siguiente, los métodos `getTitle` y `getDescription` hacen que las propiedades de objeto `title` y `description` sean accesibles en el contexto del archivo HTL:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

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

### atributo data-hidden-use {#data-sly-use-attribute}

El atributo `data-sly-use` se utiliza para inicializar la clase use dentro del código HTML. En nuestro ejemplo, el atributo `data-sly-use` declara que queremos utilizar la clase `Info`. Podemos usar sólo el nombre local de la clase porque estamos utilizando una instalación local (al colocar el archivo de origen Java en la misma carpeta que el archivo HTL). Si estuviéramos utilizando una instalación de paquete tendríamos que especificar el nombre de clase completo.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

El identificador `info` (después del punto en `data-sly-use.info`) se utiliza dentro del archivo HTL para identificar la clase. El ámbito de este identificador es global dentro del archivo, una vez declarado. No se limita al elemento que contiene la sentencia `data-sly-use`.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obteniendo propiedades {#getting-properties}

A continuación, se utiliza el identificador `info` para acceder a las propiedades de objeto `title` y `description` que se expusieron mediante los métodos de captador `Info.getTitle` y `Info.getDescription`.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Salida {#output}

Ahora, cuando accedamos a `/content/my-example.html`, devolverá el siguiente HTML:

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Más Allá De Los Conceptos Básicos {#beyond-the-basics}

En esta sección presentaremos algunas características adicionales que van más allá del sencillo ejemplo anterior:

* Pasar parámetros a una clase use.
* Clase de uso Java agrupada.
* Alternativas a `WCMUsePojo`

### Pasar parámetros {#passing-parameters}

Los parámetros se pueden pasar a una clase use tras la inicialización. Por ejemplo, podríamos hacer algo así:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Aquí se pasa un parámetro llamado `text`. A continuación, la clase use aumenta la cadena que recuperamos y muestra el resultado con `info.upperCaseText`. Esta es la clase de uso ajustada:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

    ...

    private String reverseText;

    @Override
    public void activate() throws Exception {

        ...

        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }

    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

Se accede al parámetro a través del método `WCMUsePojo` [`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

En nuestro caso, la declaración:

`get("text", String.class)`

A continuación, la cadena se invierte y se expone mediante el método:

`getReverseText()`

### Sólo pasar parámetros de data-entir-template {#only-pass-parameters-from-data-sly-template}

Aunque el ejemplo anterior es técnicamente correcto, en realidad no tiene mucho sentido pasar un valor de HTL para inicializar una clase de uso, cuando el valor en cuestión está disponible en el contexto de ejecución del código HTL (o, trivialmente, el valor es estático, como se ha indicado anteriormente).

El motivo es que la clase use siempre tendrá acceso al mismo contexto de ejecución que el código HTL. Esto trae aparejado un punto importante de prácticas recomendadas:

>[!NOTE]
>
>El paso de un parámetro a una clase use solo debe realizarse cuando la clase use se utiliza en un archivo `data-sly-template` al que se llama desde otro archivo HTL con parámetros que deben pasarse.

Por ejemplo, creemos un archivo `data-sly-template` independiente junto con nuestro ejemplo existente. Llamaremos al nuevo archivo `extra.html`. Contiene un bloque `data-sly-template` llamado `extra`:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

La plantilla `extra` toma un solo parámetro, `text`. A continuación, inicializa la clase de uso `ExtraHelper` de Java con el nombre local `extraHelper` y le pasa el valor del parámetro de plantilla `text` como parámetro de clase de uso `text`.

El cuerpo de la plantilla obtiene la propiedad `extraHelper.reversedText` (que, debajo del capó, en realidad llama a `ExtraHelper.getReversedText()`) y muestra ese valor.

También adaptamos nuestro `info.html` existente para utilizar esta nueva plantilla:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

El archivo `info.html` ahora contiene dos sentencias `data-sly-use`, la original que importa la clase de uso `Info` Java y una nueva que importa el archivo de plantilla con el nombre local `extra`.

Tenga en cuenta que podríamos haber colocado el bloque de plantilla dentro del archivo `info.html` para evitar el segundo `data-sly-use`, pero un archivo de plantilla independiente es más común y más reutilizable.

La clase `Info` se utiliza como antes, llamando a sus métodos de captador `getLowerCaseTitle()` y `getLowerCaseDescription()` a través de sus propiedades HTL correspondientes `info.lowerCaseTitle` y `info.lowerCaseDescription`.

Luego realizamos un `data-sly-call` en la plantilla `extra` y le pasamos el valor `properties.description` como parámetro `text`.

La clase de uso `Info.java` de Java se cambia para administrar el nuevo parámetro de texto:

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...

    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }

    public String getReversedText() {
        return reversedText;
    }
}
```

El parámetro `text` se recupera con `get("text", String.class)`, el valor se invierte y se pone a disposición como objeto HTL `reversedText` a través del captador `getReversedText()`.

### Clase Java agrupada {#bundled-java-class}

Con una clase de uso del paquete, la clase debe compilarse, empaquetarse e implementarse en AEM usando el mecanismo de implementación del paquete OSGi estándar. A diferencia de lo que sucede con una instalación local, la declaración de paquete de clase use **** debe denominarse normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

y, la sentencia `data-sly-use` debe hacer referencia al nombre de clase completo, en lugar de únicamente al nombre de clase local:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativas a `WCMUsePojo` {#alternatives-to-wcmusepojo}

La forma más común de crear una clase de uso de Java es extender `WCMUsePojo`. Sin embargo, hay otras opciones. Para comprender estas variantes, ayuda entender cómo funciona la sentencia HTL `data-sly-use` bajo el capó.

Supongamos que tiene la siguiente sentencia `data-sly-use`:

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

El sistema procesa la declaración de la siguiente manera:

(1)

* Si existe un archivo local `UseClass.java` en el mismo directorio que el archivo HTML, intente compilar y cargar esa clase. Si es correcto, vaya a (2).
* De lo contrario, interprete `UseClass` como un nombre de clase completo e intente cargarlo desde el entorno OSGi. Si es correcto, vaya a (2).
* De lo contrario, interprete `UseClass` como una ruta a un archivo HTML o JavaScript y cargue dicho archivo. Si tiene éxito, vaya (4).

(2)

* Intente adaptar el `Resource` actual a `UseClass`. Si se realiza correctamente, vaya a (3).
* De lo contrario, intente adaptar el `Request` actual a `UseClass`. Si se realiza correctamente, vaya a (3).
* De lo contrario, intente crear una instancia `UseClass` con un constructor de cero argumentos. Si se realiza correctamente, vaya a (3).

(3)

* Dentro de HTL, enlace el objeto recién adaptado o creado al nombre `localName`.
* Si `UseClass` implementa [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html), llame al método `init` y pase el contexto de ejecución actual (en forma de un objeto `javax.scripting.Bindings`).

(4)

* Si `UseClass` es una ruta a un archivo HTML que contiene `data-sly-template`, prepare la plantilla.
* De lo contrario, si `UseClass` es una ruta a una clase de uso de JavaScript, prepare la clase de uso (consulte [JavaScript Use-API](use-api-javascript.md)).

Algunos puntos importantes sobre la descripción anterior:

* Cualquier clase que sea adaptable desde `Resource`, adaptable desde `Request` o que tenga un constructor de argumento cero puede ser una clase de uso. La clase no tiene que ampliar `WCMUsePojo` ni siquiera implementar `Use`.
* Sin embargo, si la clase use *sí* implementa `Use`, su método `init` se llamará automáticamente con el contexto actual, permitiéndole colocar allí el código de inicialización que depende de ese contexto.
* Una clase de uso que amplía `WCMUsePojo` es sólo un caso especial de implementación `Use`. Proporciona los métodos de contexto de conveniencia y su método `activate` se llama automáticamente desde `Use.init`.

### Implementar directamente el uso de la interfaz {#directly-implement-interface-use}

Aunque la forma más común de crear una clase de uso es extender `WCMUsePojo`, también es posible implementar directamente la interfaz [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) en sí misma.

La interfaz `Use` define sólo un método:

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

Se llamará al método `init` al inicializar la clase con un objeto `Bindings` que contiene todos los objetos de contexto y cualquier parámetro que se pase a la clase use.

Todas las funcionalidades adicionales (como el equivalente de `WCMUsePojo.getProperties()`) deben implementarse explícitamente utilizando el objeto [`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html). Por ejemplo:

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties");
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

El caso principal para implementar la interfaz `Use` usted mismo en lugar de extender `WCMUsePojo` es cuando desea utilizar una subclase de una clase ya existente como la clase use.

### Adaptable desde el recurso {#adaptable-from-resource}

Otra opción es utilizar una clase auxiliar que se pueda adaptar desde `org.apache.sling.api.resource.Resource`.

Supongamos que necesita escribir una secuencia de comandos HTL que muestre el tipo MIME de un recurso DAM. En este caso, sabe que cuando se llama a su script HTL, estará dentro del contexto de un `Resource` que envuelve un JCR `Node` con nodetype `dam:Asset`.

Sabe que un nodo `dam:Asset` tiene una estructura como esta:

### Estructura de repositorio {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

Aquí mostramos el recurso (una imagen JPEG) que viene con una instalación predeterminada de AEM como parte del proyecto de ejemplo geometrixx. El recurso se denomina `jane_doe.jpg` y su tipo de mimetype es `image/jpeg`.

Para acceder al recurso desde HTL, puede declarar [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) como la clase en la sentencia `data-sly-use` y luego utilizar un método get de `Asset` para recuperar la información deseada. Por ejemplo:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

La sentencia `data-sly-use` dirige a HTL a adaptar el `Resource` actual a un `Asset` y darle el nombre local `asset`. A continuación, llama al método `getMimeType` de `Asset` mediante el método abreviado de captador HTL: `asset.mimeType`.

### Adaptable desde solicitud {#adaptable-from-request}

También es posible emplear como clase de uso cualquier clase que se pueda adaptar desde [`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)

Como en el caso anterior de una clase de uso adaptable desde `Resource`, se puede especificar una clase de uso adaptable desde [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) en la sentencia `data-sly-use`. Tras la ejecución, la solicitud actual se adaptará a la clase dada y el objeto resultante estará disponible dentro de HTL.
