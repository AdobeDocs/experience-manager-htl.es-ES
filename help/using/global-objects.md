---
title: Objetos globales HTML
seo-title: Objetos globales HTML
description: Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que normalmente estaban disponibles en JSP después de incluir global.jsp.
seo-description: 'Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que normalmente estaban disponibles en JSP después de incluir global.jsp. '
uuid: e03affbb-a683-4323-8224-53d8ef59caef
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: fe071a7e-0dae-45c1-9f86-80c558483f87
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: c3beb0d02f18483b1b000c1bf70cd59a3dcc2035

---


# Objetos globales HTML{#htl-global-objects}

Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que normalmente estaban disponibles en JSP después de incluirlos `global.jsp`. Estos objetos se suman a los que se pueden introducir a través de la [Use-API](use-api.md).

## Objetos enumerables {#enumerable-objects}

Estos objetos proporcionan un acceso cómodo a la información de uso común. Se puede acceder a su contenido con la notación de puntos y se pueden iterar usando `data-sly-list` o `data-sly-repeat`.

| Nombre de variable | Descripción |
|--- |--- |
| propiedades | Lista de propiedades del recurso actual. Respaldado por [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| pageProperties | Lista de las propiedades de página de la página actual. Respaldado por [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.hmtl) |
| hereditarioPageProperties | Lista de propiedades de página heredadas de la página actual. Respaldado por [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |


## Objetos respaldados por Java {#java-backed-objects}

Cada uno de los siguientes objetos está respaldado por el objeto Java correspondiente.

Las variables más útiles de la tabla siguiente se resaltan en negrita.

| Nombre de variable | Descripción |  |
|---|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |  |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |  |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |  |
| `currentNode` | `javax.jcr.Node` |  |
| `currentPage` | `com.day.cq.wcm.api.Page` |  |
| `currentSession` | `javax.servlet.http.HttpSession` |  |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |  |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |  |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |  |
| `log` | `org.slf4j.Logger` |  |
| `out` | `java.io.PrintWriter` |  |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |  |
| `reader` | `java.io.BufferedReader` |  |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |  |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |  |
| `resource` | `org.apache.sling.api.resource.Resource` |  |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |  |
| `resourcePage` | `com.day.cq.wcm.api.Page` |  |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |  |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |  |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |  |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |  |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |  |

## Objetos respaldados por JavaScript {#javascript-backed-objects}

También hay objetos disponibles que están respaldados por JavaScript. Sin embargo, a partir de AEM 6.2, estos objetos siguen siendo experimentales y es mejor utilizar los objetos respaldados por Java, que permiten hacer lo mismo.

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementions of all the Java objects listed below). To write HTL code that is protable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
<ul> 
 <li>wcm
  <ul> 
   <li>currentPage</li> 
   <li>nativePage: [com.day.cq.wcm.apiPage]</li> 
   <li>properties: {<i>enumerable</i>}</li> 
  </ul> </li> 
 <li>granite
  <ul> 
   <li>request
    <ul> 
     <li>parameters: {<i>enumerable</i>}</li> 
     <li>nativeRequest: [org.apache.sling.scripting.core.impl.helper.OnDemandReaderRequest]</li> 
     <li>pathInfo
      <ul> 
       <li>nativePathInfo: [SlingRequestPathInfo: path='/content/geometrixx/en/jcr:content/par/text', selectorString='null', extension='html', suffix='null']</li> 
      </ul> </li> 
    </ul> </li> 
   <li>resource
    <ul> 
     <li>nativeResource: [Paragraph, path=/content/geometrixx/en/jcr:content/par/text, type=wcm/foundation/components/text, cssClass=default, column=0/0, diffInfo=[null], resource=[JcrNodeResource, type=wcm/foundation/components/text, superType=null, path=/content/geometrixx/en/jcr:content/par/text]]</li> 
     <li>path: "/content/geometrixx/en/jcr:content/par/text"</li> 
     <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
    </ul> </li> 
   <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
  </ul> </li> 
</ul> 
<p>JS specific non-HTL related variables. Present due to JS-implementaion. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
