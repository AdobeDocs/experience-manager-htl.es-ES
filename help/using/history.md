---
title: Historial de HTL
description: Para los usuarios de AEM desde hace tiempo, este documento ofrece los antecedentes de HTL, cómo reemplaza a JSP y el cambio de nombre de Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: ht
source-wordcount: '544'
ht-degree: 100%

---


# Historial de HTL {#history-of-htl}

Para los usuarios de AEM desde hace tiempo, este documento ofrece los antecedentes de HTL, cómo reemplaza a JSP y el cambio de nombre de Sightly.

## Antes conocido como Sightly {#sightly}

El lenguaje de plantilla HTML (HTL) es el sistema de plantillas del lado del servidor recomendado para HTML en Adobe Experience Manager. Ocupa el lugar de JSP (JavaServer Pages) tal y como se usaba en versiones anteriores de AEM.

## HTL sobre JSP {#htl-over-jsp}

Se recomienda que los nuevos proyectos de AEM utilicen el lenguaje de plantilla HTML, ya que ofrece múltiples ventajas en comparación con JSP. Sin embargo, para los proyectos existentes, una migración solo tiene sentido si se estima que es menos esfuerzo que mantener los JSP existentes para los próximos años.

Pero pasar a HTL no es necesariamente una opción de todo o nada, porque los componentes escritos en HTL son compatibles con los componentes escritos en JSP o ESP. Esto significa que los proyectos existentes pueden, sin problemas, utilizar HTL para los componentes nuevos, mientras que JSP para los componentes existentes.

Incluso dentro del mismo componente, los archivos HTL se pueden utilizar junto con JSP y ESP. El siguiente ejemplo muestra en la **línea 1** cómo incluir un archivo HTL de un archivo JSP y en la **línea 2** cómo se puede incluir un archivo JSP de un archivo HTL:

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Preguntas frecuentes  {#frequently-asked-questions}

Estas son algunas preguntas que suelen hacerse los desarrolladores de AEM experimentados que son nuevos en HTL.

### ¿HTL tiene limitaciones que JSP no? {#limitations}

HTL no tiene limitaciones en comparación con JSP en el sentido de que lo que se puede hacer con JSP también debería ser posible con HTL. Sin embargo, HTL es por diseño más estricto que JSP en varios aspectos. Lo que se puede lograr en un solo archivo JSP, puede que sea necesario separarlo en una clase Java o un archivo JavaScript para poder utilizarlo en HTL. Pero esto es lo que generalmente se desea para asegurar una buena separación de las preocupaciones entre la lógica y el marcado.

### ¿HTL es compatible con las bibliotecas de etiquetas JSP? {#tag-libraries}

No, pero como se muestra en la sección [Carga de bibliotecas de cliente](getting-started.md#loading-client-libraries) del documento de Introducción, las instrucciones de plantilla y llamada ofrecen un patrón similar.

### ¿Pueden ampliarse las funciones HTL en un proyecto AEM? {#extended}

No, no se puede. HTL tiene unos mecanismos de extensión potentes para reutilizar la lógica ([API de uso](#use-api-for-accessing-logic)) y el marcado (las instrucciones de plantilla y llamada), que se pueden utilizar para modular el código de los proyectos.

### ¿Cuáles son las principales ventajas de HTL sobre JSP? {#benefits}

La seguridad y la eficacia del proyecto son las principales ventajas, que se detallan en la [Información general.](overview.md)

### ¿JSP dejará de ser útil? {#go-away}

No hay planes en este sentido.

## ¿Qué hay del nombre? {#what-is-in-a-name}

En las versiones 6.0 y 6.1 de AEM, HTL se denominaba **Sightly**. Adobe lo cambió a **lenguaje de plantilla HTML** o **HTL** para aclarar para qué sirve la especificación y para alinearlo con las directrices de nomenclatura de Adobe en general. Este cambio de nombre entró en vigor en agosto de 2016 y se aplica a AEM versión 6.0 y posteriores.

>[!NOTE]
>
>Este cambio de nomenclatura no afecta al código ni a la API, por lo que la compatibilidad no se ve afectada. Para obtener más información, [consulte este vídeo de anuncio.](https://helpx.adobe.com/es/experience-manager/how-to/announce-htl.html)

Para encontrar más información acerca de HTL y un buen punto de partida, consulte la [Guía de introducción al lenguaje de plantilla HTML (HTL)](overview.md).
