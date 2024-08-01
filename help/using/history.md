---
title: Historial de HTL
description: Para los usuarios de AEM desde hace tiempo, este documento ofrece los antecedentes de HTL, cómo reemplaza a JSP y el cambio de nombre de Sightly.
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: ht
source-wordcount: '540'
ht-degree: 100%

---


# Historial de HTL {#history-of-htl}

Para los usuarios de AEM desde hace tiempo, este documento ofrece los antecedentes de HTL, cómo reemplaza a JSP y el cambio de nombre de Sightly.

## Antes conocido como Sightly {#sightly}

El lenguaje de plantilla HTML (HTL) es el sistema de plantillas del lado del servidor recomendado para HTML en Adobe Experience Manager. Ocupa el lugar de JSP (JavaServer Pages) tal y como se usaba en versiones anteriores de AEM.

## HTL sobre JSP {#htl-over-jsp}

Adobe recomienda que, para los nuevos proyectos AEM, se utilice el lenguaje de plantillas HTML. La razón es que ofrece múltiples beneficios en comparación con JSP. Sin embargo, para los proyectos existentes, una migración solo tiene sentido si se estima que es menos esfuerzo que mantener los JSP existentes para los próximos años.

Pasarse a HTL no es necesariamente una alternativa absoluta, ya que los componentes escritos en HTL son compatibles con los de JSP y ESP. Este enfoque significa que los proyectos existentes pueden utilizar HTL para los componentes nuevos sin ningún problema y se puede seguir usando JSP para los componentes existentes.

Incluso dentro del mismo componente, los archivos HTL se pueden utilizar junto con JSP y ESP. El siguiente ejemplo muestra, en la **línea 1**, la adición de un archivo HTL de un JSP, y, en la **línea 2**, la adición de un JSP de un archivo HTL.

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## Preguntas frecuentes {#frequently-asked-questions}

Los desarrolladores experimentados en AEM, pero nuevos en HTL, suelen hacer las siguientes preguntas:

### ¿Posee HTL algunas limitaciones que JSP no? {#limitations}

HTL no tiene limitaciones en comparación con JSP, en el sentido de que lo que se puede hacer con JSP también debería ser posible con HTL. Sin embargo, por su diseño en varios aspectos, HTL es más estricto que JSP. Lo que se puede lograr en un solo archivo JSP, podría ser necesario separarlo en una clase Java o un archivo de JavaScript para poder utilizarlo en HTL. Se requiere este enfoque generalmente para lograr una buena separación de las preocupaciones entre la lógica y el marcado.

### ¿Es HTL compatible con las bibliotecas de etiquetas JSP? {#tag-libraries}

No. Sin embargo, como se muestra en la sección [Carga de bibliotecas del cliente](getting-started.md#loading-client-libraries) del documento Introducción, las instrucciones de plantillas y llamadas ofrecen un patrón similar.

### ¿Pueden ampliarse las funciones HTL en un proyecto AEM? {#extended}

No. HTL tiene unos mecanismos de extensión potentes para reutilizar la lógica ([API de uso](#use-api-for-accessing-logic)) y el marcado (las instrucciones de plantilla y llamada), que se pueden utilizar para modular el código de los proyectos.

### ¿Cuáles son las principales ventajas de HTL sobre JSP? {#benefits}

La seguridad y la eficacia del proyecto son las principales ventajas, que se detallan en la [Información general](overview.md).

### ¿JavaServer Pages (JSP) entrarán en desuso? {#go-away}

No. No hay planes de dejar de utilizar JSP.

## ¿Qué ocurre con el nombre? {#what-is-in-a-name}

En las versiones 6.0 y 6.1 de AEM, HTL se denominó **Sightly**. Adobe cambió el nombre a **lenguaje de plantillas HTML** o **HTL** para aclarar la especificación y para alinearlo con las directrices de nomenclatura de Adobe en general. Este cambio de nombre entró en vigor en agosto de 2016 y se aplicó a la versión 6.0 y posteriores de AEM.

>[!NOTE]
>
>Este cambio no afecta al código, ni a la API, ni a la compatibilidad. Para obtener más información, [consulte este vídeo de anuncio](https://helpx.adobe.com/es/experience-manager/how-to/announce-htl.html).

Para obtener más información sobre HTL, consulte la [Guía de introducción al lenguaje de plantillas HTML (HTL)](overview.md).
