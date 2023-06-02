---
title: Extensiones de AEM
description: AEM ofrece extensiones de especificación de HTL para AEM para la comodidad del desarrollador.
exl-id: d78cb84d-f958-45e2-9c6c-df86a68277d5
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 100%

---

# Extensiones de AEM {#aem-extensions}

Similar a las [Extensiones de Apache Sling de la especificación de HTL](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1), AEM ofrece algunas opciones de expresión adicionales que hacen que trabajar con conceptos de AEM sea un poco más fácil directamente en los scripts HTL.

## i18n {#i18n}

Las mismas [tres opciones adicionales](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) que en Apache Sling pueden usarse junto con `i18n`:

* `locale`
* `hint`
* `basename`

Sin embargo, en AEM, la [compatibilidad con la internacionalización](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html?lang=es) para HTL se implementa con la ayuda de la API desde el paquete `com.day.cq.i18n`.

## data-sly-include {#data-sly-include}

En AEM, `data-sly-include` puede tomar una opción adicional `wcmmode` que controla el [Modo WCM](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) para el script incluido. Los valores permitidos son los nombres de las constantes de enumeración disponibles.

## data-sly-resource {#data-sly-resource}

Además de las rutas y los `Resources`, el elemento de bloque `data-sly-resource` también puede funcionar con [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) o [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) Con ambos enfoques, se debe proporcionar la propiedad de cadena `resourceName`. Su valor se utiliza para crear un [Recurso sintético](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) que se incluye en el contexto de procesamiento. El resto de las propiedades del `Record` o `Map` que se pasó a `data-sly-resource` se utilizará como propiedades `Resource` normales. Si falta la propiedad `sling:resourceType` en este mapa, se asumirá que el tipo de recurso es el valor de la [opción de expresión](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) `resourceType` o el tipo de recurso del recurso actual que administra el procesamiento.

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
