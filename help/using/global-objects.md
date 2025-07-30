---
title: Objetos globales de HTL
description: Obtenga información acerca de objetos enumerables y objetos respaldados por Java en HTL.
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
index: false
source-git-commit: a496d23277902a5cd573a6a8af770f27b0269f05
workflow-type: tm+mt
source-wordcount: '203'
ht-degree: 100%

---


# Objetos globales de HTL {#htl-global-objects}

Sin tener que especificar nada, HTL proporciona acceso a muchos objetos útiles para el desarrollador. Estos objetos se añaden a los que se pueden introducir a través de la [API de uso](java-use-api.md).

>[!NOTE]
>
>Para desarrolladores familiarizados con el desarrollo de JSP en AEM, HTL da acceso a todos los objetos que estaban disponibles comúnmente en JSP después de incluir `global.jsp`.

## Objetos enumerables {#enumerable-objects}

Estos objetos proporcionan un acceso conveniente a la información que se utiliza con más frecuencia. Se puede acceder a su contenido con la notación de puntos y se puede iterar utilizando `data-sly-list` o `data-sly-repeat`.

| Nombre de la variable | Descripción | Respaldado por |
|--- |--- |--- |
| `properties` | Lista de propiedades del recurso actual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | Lista de propiedades de página de la página actual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | Lista de propiedades de página heredadas de la página actual | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Objetos respaldados por Java {#java-backed-objects}

El objeto Java correspondiente respalda cada uno de los siguientes objetos.

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

Es posible respaldar la lógica HTL con JavaScript. Sin embargo, el método preferido o recomendado son los [modelos Sling](https://sling.apache.org/documentation/bundles/models.html).

>[!NOTE]
>
>[La API de uso de JavaScript](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#42-javascript-use-api) ha quedado obsoleta para su uso con AEM as a Cloud Service. En su lugar, use [la API de uso de Java](https://experienceleague.adobe.com/es/docs/experience-manager-htl/content/java-use-api).
>
>Para obtener más información sobre las funciones obsoletas y eliminadas, consulte las [notas de la versión de AEM as a Cloud Service](https://experienceleague.adobe.com/es/docs/experience-manager-cloud-service/content/release-notes/deprecated-removed-features).
