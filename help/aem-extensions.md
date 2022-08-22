---
title: Extensiones de AEM
description: AEM ofrece extensiones de especificación HTL para AEM para su comodidad como desarrollador.
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# Extensiones de AEM {#aem-extensions}

Similar a la variable [Extensiones de Apache Sling de la especificación HTL,](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEM ofrece algunas opciones de expresión adicionales que hacen que trabajar con conceptos AEM sea un poco más fácil directamente en los scripts HTL.

## i18n {#i18n}

Igual [tres opciones adicionales](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) como en Apache Sling puede usarse junto con `i18n`:

* `locale`
* `hint`
* `basename`

Sin embargo, en AEM, la variable [compatibilidad con internacionalización](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) para HTL se implementa con la ayuda de la API desde la `com.day.cq.i18n` paquete.

## data-sly-include {#data-sly-include}

En AEM, `data-sly-include` puede tardar un `wcmmode` que controlan el [Modo WCM](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) para la secuencia de comandos incluida. Los valores permitidos son los nombres de las constantes enum disponibles.

## data-sly-resource {#data-sly-resource}

Además de las rutas y `Resources`, el `data-sly-resource` el elemento de bloque también puede funcionar con [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) o [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Con ambos enfoques, la variable `resourceName` Se debe proporcionar la propiedad String . Su valor se utiliza para crear un [Recurso sintético](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) que se incluye en el contexto de renderización. El resto de las propiedades de la variable `Record` o `Map` que se pasó a `data-sly-resource` se utilizará como normal `Resource` propiedades. Si la variable `sling:resourceType` falta la propiedad en este mapa, se asumirá que el tipo de recurso es el valor de la variable `resourceType` [opción de expresión](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) o el tipo de recurso del recurso actual que administra la renderización.

Dadas las siguientes propiedades de mapa/registro disponibles en el ámbito del script como `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

Y dado el siguiente marcado:

```html
<div class="outer" data-sly-resource="${map}"></div>
```

Se espera el siguiente resultado:

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
