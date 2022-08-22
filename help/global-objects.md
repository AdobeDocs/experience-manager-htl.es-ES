---
title: Objetos globales de HTL
description: Obtenga información sobre objetos enumerables, objetos respaldados por Java y objetos respaldados por JavaScript en HTL.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '200'
ht-degree: 39%

---


# Objetos globales de HTL {#htl-global-objects}

Sin tener que especificar nada, HTL proporciona acceso a muchos objetos útiles para el desarrollador. Estos objetos se añaden a los que se pueden introducir a través de la [API de uso.](java-use-api.md)

>[!NOTE]
>
>Para desarrolladores familiarizados con el desarrollo de JSP en AEM, HTL proporciona acceso a todos los objetos que estaban disponibles comúnmente en JSP después de incluir `global.jsp`.

## Objetos enumerables {#enumerable-objects}

Estos objetos proporcionan un acceso conveniente a la información que se utiliza con más frecuencia. Se puede acceder al contenido con notación de puntos y se puede iterar mediante `data-sly-list` o `data-sly-repeat`.

| Nombre de la variable | Descripción | Respaldado por |
|--- |--- |--- |
| `properties` | Lista de propiedades del recurso actual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Lista de las propiedades de página de la página actual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Lista de propiedades de página heredadas de la página actual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Objetos respaldados por Java {#java-backed-objects}

Cada uno de los siguientes objetos está respaldado por el objeto Java correspondiente.

| Nombre de la variable | Descripción |
|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| `currentContentPolicy` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentContentPolicyProperties` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| `currentPage` | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| `resource` | `org.apache.sling.api.resource.Resource` |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |
| `resourcePage` | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## Objetos respaldados por JavaScript {#javascript-backed-objects}

Es posible respaldar la lógica HTL con JavaScript. Sin embargo, el método preferido o recomendado es usar [Modelos Sling.](https://sling.apache.org/documentation/bundles/models.html)
