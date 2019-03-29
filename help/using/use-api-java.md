---
title: HTL Java Use-API
seo-title: HTL Java Use-API
description: Lenguaje de plantilla HTML (HTL) La API para uso de Java permite que
  un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada.
seo-description: Lenguaje de plantilla HTML (HTL) La API para uso de Java permite
  que un archivo HTL acceda a los métodos de ayuda en una clase Java personalizada.
uuid: b 340 f 8 f 7-a 193-45 c 8-aa 39-5 c 6 e 2 c 0194 ea
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 126 ebc 9 d -5 f 7 b -47 a 4-aea 2-c 8840 d 34864 c
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 796c55d3d85e6b5a3efaa5c04a25be1b0b4e54dd

---


# HTL Java Use-API{#htl-java-use-api}

El lenguaje de uso de Java de HTML (HTL) Java permite a un archivo HTL acceder a los métodos de ayuda en una clase Java personalizada. Esto permite encapsular toda la lógica comercial compleja en el código Java, mientras que el código HTL trata únicamente la producción directa de marcas.

## Ejemplo sencillo {#a-simple-example}

Comenzaremos con un componente HTL que *no* tenga una clase de uso. Consta de un único archivo, `/apps/my-example/components/info.html`

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

También agregamos contenido para que este componente se procese en **`/content/my-example/`**:

### `http://localhost:4502/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

Cuando se accede a este contenido, se ejecuta el archivo HTL. Dentro del código HTL utilizamos el objeto contextual****`properties`para acceder al recurso actual `title` y `description` mostrarlo. El HTML de salida será:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### Adición de una clase de uso {#adding-a-use-class}

El **componente de información** tal como está no necesita una clase de uso para realizar su función (muy sencilla). Sin embargo, existen casos en los que necesita hacer cosas que no se pueden hacer en HTL, por lo que necesita una clase de uso. Sin embargo, recuerde lo siguiente:

>[!NOTE]
>
>*Una clase de uso solo debe usarse cuando algo no se pueda hacer en HTL.*

Por ejemplo, supongamos que desea que `info` el componente muestre las propiedades `title` y **`description`** las propiedades del recurso, pero todas en letra minúscula. Como HTL no tiene un método para cadenas bajas, necesitará una clase de uso. Podemos hacerlo agregando una clase de uso Java y cambiando lo **`info.html`** siguiente:

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

En las secciones siguientes, recorreremos las distintas partes del código.

### Clase local de Java vs. {#local-vs-bundle-java-class}

La clase de uso Java se puede instalar de dos maneras: **local** o **paquete**. *Este ejemplo utiliza una instalación local.*

En una instalación local, el archivo de origen Java se coloca junto al archivo HTL, en la misma carpeta del repositorio. La fuente se compilará automáticamente bajo demanda. No se requiere un paso de compilación o paquete independiente.

En una instalación del paquete, la clase Java debe compilarse e implementarse dentro de un paquete osgi mediante el mecanismo de implementación de paquete AEM estándar (consulte [la clase Java compilada](#bundled-java-class)).

>[!NOTE]
>
>Se recomienda utilizar una **clase local** de Java cuando la clase use es específica del componente en cuestión.
>
>Se recomienda utilizar un **paquete** de uso Java cuando el código Java implemente un servicio al que se accede desde varios componentes HTL.

### Java package is repository path {#java-package-is-repository-path}

Cuando se utiliza una instalación local, el nombre del paquete de la clase de uso debe coincidir con el de la ubicación de la carpeta del repositorio, con cualquier guión en la ruta reemplazado por guiones bajos en el nombre del paquete.

In this case **`Info.java`** is located at **`/apps/my-example/components/info`** so the package is **`apps.my_example.components.info`** :

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
>El uso de guiones en los nombres de los elementos del repositorio es una práctica recomendada en el desarrollo de AEM. Sin embargo, los guiones no son válidos dentro de los nombres de paquetes Java. Por este motivo **, todos los guiones de la ruta del repositorio deben convertirse en guiones bajos en el nombre del paquete**.

### Ampliación `WCMUsePojo`{#extending-wcmusepojo}

Aunque hay un número de formas de incorporar una clase Java con HTL (consulte Alternativas a `WCMUsePojo`), la más sencilla es ampliar `WCMUsePojo` la clase:

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    
    ...
}
```

### Inicializando la clase {#initializing-the-class}

Cuando se amplía la clase use desde **`WCMUsePojo`**, la inicialización se realiza anulando el **`activate`** método:

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

Normalmente, el [método de activación](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html) se utiliza para precalcular y almacenar (en variables de miembro) los valores necesarios en el código HTL, según el contexto actual (la solicitud y el recurso actuales, por ejemplo).

`WCMUsePojo` La clase proporciona acceso al mismo conjunto de objetos de contexto que están disponibles en un archivo HTL (consulte [Objetos globales](global-objects.md)).

En una clase que se extiende **`WCMUsePojo`**, se puede acceder a los objetos de contexto *por nombre* mediante

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

Alternativamente, el método de conveniencia adecuado **puede acceder directamente a los objetos de contexto usados con más frecuencia**:

|  |
|---|---|
| [Pagemanager](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [Getpagemanager ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getcurrentpage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Página](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [Getresourcepage ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getpageproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Diseñador](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [Getdesigner ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Diseño](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [Getcurrentdesign ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Estilo](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [Getcurrentstyle ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Componente](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [Getcomponent ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [Valuemap](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [Getinheritedproperties ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse#getInheritedProperties.html()) |
| [Medio](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [Getresource ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [Resourceresolver](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [Getresourceresolver ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [Slinghttpservletrequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [Getrequest ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [Slinghttpservletresponse](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [Getresponse ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [Slingscripthelper](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [Getslingscripthelper ()](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### Métodos de Captador {#getter-methods}

Una vez iniciada la clase de uso, se ejecuta el archivo HTL. Durante esta fase, HTL generalmente obtendrá el estado de diversas variables de miembro de la clase de uso y las procesará para su presentación.

Para proporcionar acceso a estos valores desde el archivo HTL, debe definir métodos de captador personalizado en la clase de uso **de acuerdo con la siguiente convención de nombre**:

* Un método del formulario **`getXyz`** mostrará en el archivo HTL una propiedad de objeto llamada ***xyz***.

Por ejemplo, en el ejemplo siguiente, los métodos **`getTitle`** y **`getDescription`** el resultado en las propiedades **`title`** de objeto y **`description`** son accesibles en el contexto del archivo HTL:

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

### atributo de uso oscuro {#data-sly-use-attribute}

El **`data-sly-use`** atributo se utiliza para inicializar la clase de uso dentro del código HTL. En nuestro ejemplo, el `data-sly-use` atributo declara que queremos utilizar la clase **`Info`**. Solo podemos utilizar el nombre local de la clase porque utilizamos una instalación local (hemos colocado el archivo de origen Java en la misma carpeta que el archivo HTL). Si utilizáramos una instalación del paquete, tendríamos que especificar el nombre de clase completo (consulte [la instalación del paquete de clases usadas](#LocalvsBundleJavaClass)).

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Identificador local {#local-identifier}

El identificador "**`info`**(después del punto en **`data-sly-use.info`**) se utiliza dentro del archivo HTL para identificar la clase. El ámbito de este identificador es global dentro del archivo, después de haber sido declarado. No se limita al elemento que contiene `data-sly-use` la instrucción.

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Obtención de propiedades {#getting-properties}

El identificador `info` se utiliza entonces para acceder a las propiedades **`title`** del objeto y **`description`** que se han expuesto mediante métodos de captador `Info.getTitle` y **`Info.getDescription`**.

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### Salida {#output}

Ahora, **`/content/my-example.html`** cuando tengamos acceso, devolverá el siguiente HTML:

### `view-source:http://localhost:4502/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## Más allá de los conceptos básicos {#beyond-the-basics}

En esta sección introdobamos algunas funciones adicionales que van más allá del ejemplo sencillo de arriba:

* Pasar parámetros a una clase de uso.
* Se agrupó la clase de uso Java.
* Alternativas `WCMUsePojo`

### Pasar parámetros {#passing-parameters}

Los parámetros se pueden pasar a una clase de uso tras la inicialización. Por ejemplo, podemos hacer algo similar a esto:

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

Aquí vamos a pasar un parámetro denominado **`text`**. La clase use luego mayúscula con la cadena que recuperamos y mostramos el resultado con `info.upperCaseText`. Esta es la clase de uso ajustada:

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

En nuestro caso, la sentencia

`get("text", String.class)`

La cadena se invierte y expone a través del método

**`getReverseText()`**

### Pasar solo parámetros a partir de una plantilla de datos {#only-pass-parameters-from-data-sly-template}

Aunque el ejemplo anterior es técnicamente correcto, en realidad no tiene sentido pasar un valor de HTL para inicializar una clase de uso, cuando el valor en cuestión está disponible en el contexto de ejecución del código HTL (o, trivialmente, el valor es estático, como se muestra más arriba).

El motivo es que la clase de uso siempre tendrá acceso al mismo contexto de ejecución que el código HTL. Esto abre un punto de importación de optimizaciones:

>[!NOTE]
>
>Pasar un parámetro a una clase de uso solo debe realizarse cuando la clase use se utiliza en un **archivo de plantilla-infinita de datos** que se llama desde otro archivo HTL con parámetros que deben pasarse.

Por ejemplo, vamos a crear un archivo independiente `data-sly-template` junto con nuestro ejemplo existente. Llamaremos al nuevo archivo `extra.html`. Contiene un **`data-sly-template`** bloque llamado **`extra`**:

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

The template **`extra`**, takes a single parameter, **`text`**. A continuación, inicializa la clase de uso `ExtraHelper` Java con el nombre **`extraHelper`** local y lo pasa el valor del parámetro de plantilla **`text`** como parámetro **`text`**de clase de uso.

El cuerpo de la plantilla obtiene la propiedad `extraHelper.reversedText` (que, bajo el carácter, en realidad llama `ExtraHelper.getReversedText()`) y muestra ese valor.

También adaptamos nuestra existente **`info.html`** para utilizar esta nueva plantilla:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info" 
     data-sly-use.extra="extra.html">
    
  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>
    
  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

El archivo `info.html` ahora contiene dos **`data-sly-use`** sentencias, la original que importa la **`Info`** clase de uso Java y otra nueva que importa el archivo de plantilla bajo el nombre `extra`local.

Tenga en cuenta que podemos haber colocado el bloque de plantilla dentro del **`info.html`** archivo para evitar la segunda **`data-sly-use`**, pero un archivo de plantilla independiente es más común y más reutilizable.

La **`Info`** clase se emplea como antes, llama a sus métodos **`getLowerCaseTitle()`** de captador y `getLowerCaseDescription()` a sus propiedades HTL correspondientes `info.lowerCaseTitle` y **`info.lowerCaseDescription`**.

A continuación, realizamos una **`data-sly-call`** operación a la plantilla **`extra`** y pasaremos el valor `properties.description` como parámetro **`text`**.

La clase de uso `Info.java` Java se cambia para gestionar el nuevo parámetro de texto:

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

El **`text`** parámetro se recupera con **`get("text", String.class)`**, el valor se invierte y pone a disposición como objeto HTL `reversedText` a través del captador `getReversedText()`.

### Clase Java compilada {#bundled-java-class}

Con un paquete de uso, la clase debe compilarse, empaquetarse e implementarse en AEM mediante el mecanismo de implementación de paquete osgi estándar. A diferencia de una instalación local, la declaración **del paquete de la clase use** debe llamarse normalmente:

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;
 
import com.adobe.cq.sightly.WCMUsePojo;
 
public class Info extends WCMUsePojo {
    ...
}
```

y, la `data-sly-use` sentencia debe hacer referencia al *nombre*de clase completo, en vez de solo al nombre de clase local:

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### Alternativas `WCMUsePojo`{#alternatives-to-wcmusepojo}

La manera más común de crear una clase de uso Java es ampliar `WCMUsePojo`. Sin embargo, existen varias opciones. Para comprender estas variantes, ayuda a comprender cómo funciona la `data-sly-use` declaración HTL bajo el estado.

Suponga que tiene `data-sly-use` la siguiente instrucción:

**`<div data-sly-use.`** `localName`**`="`** `UseClass`**`">`**

El sistema procesa la sentencia de la siguiente manera:

(1)

* Si existe un archivo local *useclass. java* en el mismo directorio que el archivo HTL, intente compilar y cargar esa clase. Si se dirige correctamente a (2).

* De lo contrario, interprete *useclass* como nombre de clase completo e intente cargarlo desde el entorno osgi. Si se dirige correctamente a (2).
* De lo contrario, interprete *useclass* como ruta a un archivo HTL o JavaScript y cargue ese archivo. If success goto (4).

(2)

* Intente adaptar el actual **`Resource`** a *`UseClass.`* Si es correcto, vaya a (3).

* En caso contrario, intente adaptar el actual **`Request`** a *`UseClass`*. Si es correcto, vaya a (3).

* En caso contrario, intente crear instancias *`UseClass`* con un constructor de argumento cero. Si es correcto, vaya a (3).

(3)

* En HTL, enlace el objeto recién adaptado o creado al nombre *`localName`*.
* Si *`UseClass`* implementa [`io.sightly.java.api.Use`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) , llame al `init` método, pasando el contexto de ejecución actual (en forma de `javax.scripting.Bindings` objeto).

(4)

* Si *`UseClass`* es una ruta a un archivo HTL que contiene a `data-sly-template`, prepare la plantilla.

* De lo contrario, si *`UseClass`* es una ruta a una clase de uso JavaScript, prepare la clase de uso (consulte [Uso API de uso de JavaScript](use-api-javascript.md)).

Algunos puntos importantes sobre la descripción anterior:

* Cualquier clase que se adapte desde `Resource`, adaptable o `Request`que tenga un constructor de argumento cero puede ser una clase de uso. La clase no tiene que ampliarse ni `WCMUsePojo` siquiera implementarse `Use`.

* Sin embargo, si la clase de uso *implementa*`Use`, su **`init`** método se llamará automáticamente con el contexto actual, lo que permite colocar aquí el código de inicialización que depende de ese contexto.

* Una clase de uso que se extiende `WCMUsePojo` es solo un caso especial de implementación **`Use`**. Proporciona los métodos de contexto de conveniencia y su **`activate`** método se llama automáticamente desde `Use.init`.

### Implementar directamente la interfaz de la interfaz {#directly-implement-interface-use}

Aunque la manera más común de crear una clase de uso es ampliar `WCMUsePojo`, también es posible implementar directamente la `[io.sightly.java.api.Use](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html)`interfaz en sí misma.

`Use` La interfaz define sólo un método:

`[public void init(javax.script.Bindings bindings)](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))`

Se llamará al **`init`** método durante la inicialización de la clase con un **`Bindings`** objeto que contenga todos los objetos de contexto y cualquier parámetro que se pase a la clase de uso.

Toda la funcionalidad adicional (como el equivalente de `WCMUsePojo.getProperties()`) debe agregarse explícitamente mediante ` [javax.script.Bindings](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html)` el objeto. Por ejemplo:

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

El caso principal para implementar `Use` la interfaz usted mismo en lugar de ampliarlo `WCMUsePojo` es cuando se desea utilizar una subclase de una clase existente como la de uso.

### Adaptable desde Resource {#adaptable-from-resource}

Otra opción es utilizar una clase de ayuda a partir **`org.apache.sling.api.resource.Resource`**de la cual se puede adaptar.

Supongamos que necesita escribir una secuencia de comandos HTL que muestre el mimetype de un recurso DAM. En este caso, sabrá que cuando se llama a su secuencia de comandos HTL, se encuentra en el contexto de un **`Resource`** que envuelve un JCR **`Node`** con nodetype **`dam:Asset`**.

Usted sabe que un **`dam:Asset`** nodo tiene una estructura como ésta:

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

Aquí mostramos el recurso (imagen JPEG) que viene con una instalación predeterminada de AEM como parte del proyecto de ejemplo geometrixx. El recurso se llama **`jane_doe.jpg`** y su tipo de letra **`image/jpeg`**es el mimetype.

Para acceder al recurso desde HTL, puede declarar ` [com.day.cq.dam.api.Asset](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html)` como clase en **`data-sly-use`** la sentencia: y, a continuación, utilice un método get para **`Asset`** recuperar la información que desee. Por ejemplo:

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

La `data-sly-use` sentencia dirige HTL para adaptar el actual **`Resource`** a uno **`Asset`** y darle el nombre **`asset`**local. A continuación, llama al **`getMimeType`** método de `Asset` uso de la abreviación de getl HTL: `asset.mimeType`.

### Adaptable desde solicitud {#adaptable-from-request}

También es posible ubicar como una clase de uso cualquier clase que se pueda adaptar desde **` [org.apache.sling.api.SlingHttpServletRequest](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`**

Como sucede con el caso anterior de una clase de uso adaptable desde `Resource`, se puede especificar en [`SlingHttpServletRequest`](https://helpx.adobe.com/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html)`data-sly-use` la sentencia una clase de uso adaptable. Tras la ejecución, la solicitud actual se adaptará a la clase dada y el objeto resultante estará disponible en HTL.
