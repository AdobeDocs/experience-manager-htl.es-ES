---
title: HTL Java Use-API
seo-title: HTL Java Use-API
description: 'Lenguaje de plantilla HTML - HTL - La API para uso de Java permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada. '
seo-description: 'Lenguaje de plantilla HTML - HTL - La API para uso de Java permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada. '
uuid: b340f8f7-a193-45c8-aa39-5c6e2c0194ea
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 126ebc9d-5f7b-47a4-aea2-c8840d34864c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 6de5ed20e4463c0c2e804e24cb853336229a7c1f

---


# HTL Java Use-API{#htl-java-use-api}

La API de uso de Java del lenguaje de plantilla HTML (HTL) permite que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada. Esto permite encapsular toda la lógica empresarial compleja en el código Java, mientras que el código HTL sólo se ocupa de la producción directa de marcado.

## Un ejemplo sencillo {#a-simple-example}

Comenzaremos con un componente HTL que *no* tenga una clase de uso. Consiste en un solo archivo, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

También agregamos contenido para este componente para procesar en **`/content/my-example/`**:

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Cuando se accede a este contenido, se ejecuta el archivo HTML. En el código HTML utilizamos el objeto de contexto **`properties`** para acceder al recurso actual `title` y `description` mostrarlo. El HTML de salida será:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Adición de una clase de uso {#adding-a-use-class}

El componente **info** tal como está no necesita una clase use para realizar su función (muy simple). Sin embargo, hay casos en los que se necesita hacer cosas que no se pueden hacer en HTL y por lo tanto se necesita una clase de uso. Pero tenga en cuenta lo siguiente:

>[!NOTE]
>
>*Una clase de uso sólo debe usarse cuando no se pueda hacer algo solo en HTL.*

Por ejemplo, supongamos que desea que el `info` componente muestre las propiedades `title` y **`description`** del recurso, pero todo en minúsculas. Dado que HTL no tiene un método para quitar cadenas, necesitará una clase use. Podemos hacerlo agregando una clase de uso de Java y cambiando la **`info.html`** siguiente:

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

La clase de uso de Java se puede instalar de dos maneras: **local** o **paquete**. *En este ejemplo se utiliza una instalación local.*

En una instalación local, el archivo de origen Java se coloca junto al archivo HTL, en la misma carpeta de repositorio. La fuente se compila automáticamente a petición. No se requiere una compilación o un paso de embalaje por separado.

En una instalación del paquete, la clase Java debe compilarse e implementarse dentro de un paquete OSGi mediante el mecanismo de implementación del paquete AEM estándar (consulte Clase [Java](#bundled-java-class)agrupada).

>[!NOTE]
>
>Se recomienda una clase **de uso Java** local cuando la clase de uso sea específica del componente en cuestión.
>
>Se recomienda una clase **de uso Java de** paquete cuando el código Java implemente un servicio al que se accede desde varios componentes HTL.

### El paquete Java es la ruta del repositorio {#java-package-is-repository-path}

Cuando se utiliza una instalación local, el nombre del paquete de la clase use debe coincidir con el de la ubicación de la carpeta del repositorio, reemplazando cualquier guión de la ruta por guiones bajos en el nombre del paquete.

En este caso **`Info.java`** se encuentra en **`/apps/my-example/components/info`** así que el paquete es **`apps.my_example.components.info`** :

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
>El uso de guiones en los nombres de los elementos del repositorio es una práctica recomendada en el desarrollo de AEM. Sin embargo, los guiones son ilegales en los nombres de paquetes de Java. Por este motivo, **todos los guiones de la ruta del repositorio deben convertirse en guiones bajos en el nombre** del paquete.

### Ampliación `WCMUsePojo`{#extending-wcmusepojo}

Aunque hay varias formas de incorporar una clase Java con HTL (consulte Alternativas a `WCMUsePojo`), la más simple es ampliar la `WCMUsePojo` clase:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### Inicialización de la clase {#initializing-the-class}

Cuando se amplía la clase use de **`WCMUsePojo`**, la inicialización se realiza anulando el **`activate`** método:

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

Normalmente, el método [activate](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) se utiliza para precalcular y almacenar (en variables de miembros) los valores necesarios en el código HTML, según el contexto actual (la solicitud y el recurso actuales, por ejemplo).

La `WCMUsePojo` clase proporciona acceso al mismo conjunto de objetos de contexto que están disponibles en un archivo HTML (consulte Objetos [](global-objects.md)globales).

En una clase que se extiende **`WCMUsePojo`**, se puede acceder a los objetos de contexto *por nombre* mediante

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

También se puede acceder directamente a los objetos de contexto más utilizados mediante el método **de** conveniencia adecuado:

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Diseñador](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Diseño](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Estilo](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [Medio](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Métodos de recopilación {#getter-methods}

Una vez inicializada la clase use, se ejecuta el archivo HTL. Durante esta etapa, HTL generalmente extrae el estado de varias variables de miembros de la clase use y las procesa para su presentación.

Para proporcionar acceso a estos valores desde el archivo HTL, debe definir métodos de captador personalizados en la clase use **según la siguiente convención** de nomenclatura:

* Un método del formulario **`getXyz`** mostrará en el archivo HTL una propiedad de objeto llamada ***xyz***.

Por ejemplo, en el ejemplo siguiente, los métodos **`getTitle`** y **`getDescription`** resultan en propiedades de objeto **`title`** y en **`description`** ser accesibles en el contexto del archivo HTL:

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

### atributo data-stir-use {#data-sly-use-attribute}

El **`data-sly-use`** atributo se utiliza para inicializar la clase use dentro del código HTML. En nuestro ejemplo, el `data-sly-use` atributo declara que queremos utilizar la clase **`Info`**. Podemos usar sólo el nombre local de la clase porque estamos utilizando una instalación local (al colocar el archivo de origen Java en la misma carpeta que el archivo HTML). Si estuviéramos utilizando una instalación de paquete, tendríamos que especificar el nombre de clase completo (consulte Instalación [de paquete de clase de](#LocalvsBundleJavaClass)uso).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

El identificador '**`info`**' (después del punto en **`data-sly-use.info`**) se utiliza dentro del archivo HTML para identificar la clase. El ámbito de este identificador es global dentro del archivo, una vez declarado. No se limita al elemento que contiene la `data-sly-use` instrucción.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtención de propiedades {#getting-properties}

A continuación, el identificador `info` se utiliza para acceder a las propiedades de objeto **`title`** y **`description`** que se expusieron a través de los métodos `Info.getTitle` y **`Info.getDescription`** captador.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Salida {#output}

Ahora, cuando accedamos a **`/content/my-example.html`** él, se devuelve el siguiente HTML:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Más Allá De Lo Básico {#beyond-the-basics}

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

Aquí se pasa un parámetro llamado **`text`**. A continuación, la clase use aumenta la cadena que recuperamos y muestra el resultado con `info.upperCaseText`. Esta es la clase de uso ajustada:

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

Se accede al parámetro a través del `WCMUsePojo` método

[ `<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

En nuestro caso, la declaración

`get("text", String.class)`

La cadena se invierte y se expone mediante el método

**`getReverseText()`**

### Sólo pasar parámetros desde data-entir-template {#only-pass-parameters-from-data-sly-template}

Aunque el ejemplo anterior es técnicamente correcto, en realidad no tiene mucho sentido pasar un valor de HTL para inicializar una clase de uso, cuando el valor en cuestión está disponible en el contexto de ejecución del código HTL (o, trivialmente, el valor es estático, como se ha indicado anteriormente).

El motivo es que la clase use siempre tendrá acceso al mismo contexto de ejecución que el código HTL. Esto trae aparejado un punto importante de prácticas recomendadas:

>[!NOTE]
>
>El paso de un parámetro a una clase use solo debe realizarse cuando la clase use se utiliza en un archivo de plantilla **** data-stir que se llama desde otro archivo HTL con parámetros que deben pasarse.

Por ejemplo, creemos un `data-sly-template` archivo independiente junto con nuestro ejemplo existente. Llamaremos al nuevo archivo `extra.html`. Contiene un **`data-sly-template`** bloque llamado **`extra`**:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

La plantilla **`extra`**, toma un solo parámetro, **`text`**. A continuación, inicializa la clase use-class de Java `ExtraHelper` con el nombre local **`extraHelper`** y le pasa el valor del parámetro de plantilla **`text`** como parámetro use-class **`text`**.

El cuerpo de la plantilla obtiene la propiedad `extraHelper.reversedText` (que, debajo del capó, en realidad llama `ExtraHelper.getReversedText()`) y muestra ese valor.

También adaptamos nuestra plantilla existente **`info.html`** para utilizar esta nueva plantilla:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

El archivo `info.html` ahora contiene dos **`data-sly-use`** sentencias, la original que importa la clase de uso de **`Info`** Java y una nueva que importa el archivo de plantilla con el nombre local `extra`.

Tenga en cuenta que podríamos haber colocado el bloque de plantilla dentro del **`info.html`** archivo para evitar el segundo **`data-sly-use`**, pero un archivo de plantilla independiente es más común y más reutilizable.

La **`Info`** clase se emplea como antes, llamando a sus métodos de captador **`getLowerCaseTitle()`** y `getLowerCaseDescription()` a través de sus correspondientes propiedades `info.lowerCaseTitle` y **`info.lowerCaseDescription`** HTL.

Luego realizamos un **`data-sly-call`** a la plantilla **`extra`** y le pasamos el valor `properties.description` como parámetro **`text`**.

La clase de uso Java `Info.java` se cambia para administrar el nuevo parámetro de texto:

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

El **`text`** parámetro se recupera con **`get("text", String.class)`**, el valor se invierte y se pone a disposición como objeto HTL `reversedText` a través del captador `getReversedText()`.

### Clase Java agrupada {#bundled-java-class}

Con una clase de uso del paquete, la clase debe compilarse, empaquetarse e implementarse en AEM mediante el mecanismo de implementación del paquete OSGi estándar. A diferencia de una instalación local, la declaración **de** paquete de clase de uso debe denominarse normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

y, la `data-sly-use` instrucción debe hacer referencia al nombre *de clase* completo, en lugar de únicamente al nombre de clase local:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativas a `WCMUsePojo`{#alternatives-to-wcmusepojo}

La forma más común de crear una clase de uso de Java es ampliarla `WCMUsePojo`. Sin embargo, hay otras opciones. Para comprender estas variantes ayuda a entender cómo funciona la afirmación HTL `data-sly-use` debajo del capó.

Supongamos que tiene la siguiente `data-sly-use` afirmación:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

El sistema procesa la declaración de la siguiente manera:

(1)

* Si existe un archivo local *UseClass.java* en el mismo directorio que el archivo HTML, intente compilar y cargar esa clase. Si es correcto, vaya a (2).

* De lo contrario, interprete *UseClass* como un nombre de clase completo e intente cargarlo desde el entorno OSGi. Si es correcto, vaya a (2).
* De lo contrario, interprete *UseClass* como una ruta a un archivo HTML o JavaScript y cargue dicho archivo. Si tiene éxito, vaya (4).

(2)

* Intente adaptar la corriente **`Resource`** a *`UseClass.`* Si tiene éxito, vaya a (3).

* De lo contrario, intente adaptar la corriente **`Request`** a *`UseClass`*. Si se realiza correctamente, vaya a (3).

* De lo contrario, intente crear una instancia *`UseClass`* con un constructor de cero argumentos. Si se realiza correctamente, vaya a (3).

(3)

* En HTL, enlace el objeto recién adaptado o creado al nombre *`localName`*.
* Si *`UseClass`* implementa [ entonces llame al `io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) método, pasando el contexto de ejecución actual (en forma de `init` `javax.scripting.Bindings` objeto).

(4)

* Si *`UseClass`* es una ruta a un archivo HTML que contiene un `data-sly-template`, prepare la plantilla.

* De lo contrario, si *`UseClass`* es una ruta a una clase de uso de JavaScript, prepare la clase de uso (consulte [JavaScript Use-API](use-api-javascript.md)).

Algunos puntos importantes sobre la descripción anterior:

* Cualquier clase que sea adaptable desde `Resource`, adaptable desde `Request`, o que tenga un constructor de cero argumentos puede ser una clase de uso. La clase no tiene por qué ampliar `WCMUsePojo` o incluso implementar `Use`.

* Sin embargo, si la clase use *no implementa* `Use`, se llamará automáticamente a su **`init`** método con el contexto actual, permitiéndole colocar allí el código de inicialización que depende de ese contexto.

* Una clase de uso que se extiende `WCMUsePojo` es sólo un caso especial de implementación **`Use`**. Proporciona los métodos de contexto de conveniencia y su **`activate`** método se llama automáticamente desde `Use.init`.

### Implementar directamente el uso de la interfaz {#directly-implement-interface-use}

Aunque la forma más común de crear una clase de uso es ampliarla `WCMUsePojo`, también es posible implementar directamente la propia `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`interfaz.

La `Use` interfaz define sólo un método:

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

Se llamará al **`init`** método al inicializar la clase con un **`Bindings`** objeto que contenga todos los objetos de contexto y cualquier parámetro que se pase a la clase use.

Todas las funcionalidades adicionales (como el equivalente de `WCMUsePojo.getProperties()`) deben implementarse explícitamente utilizando el ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` objeto. Por ejemplo:

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

El caso principal para implementar la `Use` interfaz usted mismo en lugar de extenderla `WCMUsePojo` es cuando desea utilizar una subclase de una clase ya existente como clase de uso.

### Adaptable desde recurso {#adaptable-from-resource}

Otra opción es utilizar una clase auxiliar que se pueda adaptar desde **`org.apache.sling.api.resource.Resource`**.

Supongamos que necesita escribir un script HTL que muestre el tipo MIME de un recurso DAM. En este caso, sabe que cuando se llama a su script HTL, estará dentro del contexto de un **`Resource`** JCR **`Node`** con nodetype **`dam:Asset`**.

Ya sabe que un **`dam:Asset`** nodo tiene una estructura como esta:

### Estructura del repositorio {#repository-structure}

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

Aquí se muestra el recurso (una imagen JPEG) que viene con una instalación predeterminada de AEM como parte del proyecto de ejemplo geometrixx. Se llama al recurso **`jane_doe.jpg`** y su tipo MIME es **`image/jpeg`**.

Para acceder al recurso desde HTL, puede declarar ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` como clase en la **`data-sly-use`** instrucción: y, a continuación, utilice un método get de **`Asset`** para recuperar la información deseada. Por ejemplo:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

La `data-sly-use` declaración ordena a HTL adaptar la corriente **`Resource`** a una **`Asset`** y darle el nombre local **`asset`**. Luego llama al **`getMimeType`** método de `Asset` usar la abreviatura de captador HTL: `asset.mimeType`.

### Adaptable desde solicitud {#adaptable-from-request}

También es posible emplear como clase de uso cualquier clase que se pueda adaptar desde **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

Como en el caso anterior de una clase de uso adaptable de `Resource`, en la [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) instrucción se puede especificar una clase de uso adaptable de `data-sly-use` . Tras la ejecución, la solicitud actual se adaptará a la clase dada y el objeto resultante estará disponible en HTL.
