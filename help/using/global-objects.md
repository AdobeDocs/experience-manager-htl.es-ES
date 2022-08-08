---
title: Objetos globales de HTL
description: Obtenga información acerca objetos enumerables, objetos respaldados por Java y objetos respaldados por JavaScript. Busque los nombres de las variables y las descripciones para estos objetos globales de HTL utilizados en AEM.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 7b53eff0652f650ffb8caae0e69aa349b5c548eb
workflow-type: ht
source-wordcount: '223'
ht-degree: 100%

---

# Objetos globales de HTL {#htl-global-objects}

Sin tener que especificar nada, HTL proporciona acceso a todos los objetos que normalmente estaban disponibles en JSP después de incluir `global.jsp`. Estos objetos se añaden a los que se pueden introducir a través de la [API de uso](use-api.md).

## Objetos enumerables {#enumerable-objects}

Estos objetos proporcionan un acceso conveniente a la información que se utiliza con más frecuencia. Se puede acceder a su contenido con la notación de puntos y se pueden iterar utilizando `data-sly-list` o `data-sly-repeat`.

| Nombre de la variable | Descripción |
|--- |--- |
| `properties` | Lista de propiedades del recurso actual. Respaldado por [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Lista de las propiedades de página de la página actual. Respaldado por [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Lista de propiedades de página heredadas de la página actual. Respaldado por [org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Objetos respaldados por Java {#java-backed-objects}

Cada uno de los siguientes objetos está respaldado por el objeto Java correspondiente.

Las variables más útiles de la tabla siguiente se resaltan en negrita.

| Nombre de la variable | Descripción |
|---|---|
| **`component`** | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| **`currentDesign`** | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| **`currentPage`** | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| **`request`** | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| **`resource`** | `org.apache.sling.api.resource.Resource` |
| **`resourceDesign`** | `com.day.cq.wcm.api.designer.Design` |
| **`resourcePage`** | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| **`wcmmode`** | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## Objetos respaldados por JavaScript {#javascript-backed-objects}

Es posible respaldar la lógica HTL con JavaScript. Sin embargo, el método preferido o recomendado es usar [Modelos Sling.](https://sling.apache.org/documentation/bundles/models.html)

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementations of all the Java objects listed below). To write HTL code that is portable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
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
<p>JS specific non-HTL related variables. Present due to JS-implementation. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
