---
title: AEM HTL Overview
seo-title: Overview of AEM HTL technical documentation.
description: The purpose of HTL supported by AEM, is to offer a highly productive enterprise-level web framework that increases security, and allows HTML developers without Java knowledge to better participate in AEM projects.
seo-description: This document lays out the principles and purpose of HTML Template Language - HTL - supported by Adobe Experience Manager. HTL is a highly productive enterprise-level web framework that increases security, and allows HTML developers without Java knowledge to better participate in AEM projects.
uuid: 8f486325-0a1b-4186-a998-96fc0034c44a
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: introducción
content-type: referencia
discoiquuid: 8f779e08-94c7-43bc-a6e5-d81a9f818c5c
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
skyline: prueba de replicación
translation-type: tm+mt
source-git-commit: 0aa1e905fd6d24f7031dceb0a8a89b56da198719

---


# Información general {#overview}

The purpose of HTML Template Language (HTL), supported by Adobe Experience Manager (AEM), is to offer a highly productive enterprise-level web framework that increases security, and allows HTML developers without Java knowledge to better participate in AEM projects.

El lenguaje de plantilla HTML se ha introducido con AEM 6.0 y sustituye a JSP (páginas de JavaServer) como sistema de plantilla preferido y recomendado en el servidor para HTML. For web developers who need to build robust enterprise websites, the HTML Template Language helps to achieve increased security and development efficiency.

## Increased Security {#increased-security}

El lenguaje de plantilla HTML aumenta la seguridad de los sitios que lo utilizan en su implementación, en comparación con JSP y con la mayoría de los demás sistemas de plantilla, ya que HTL es capaz de aplicar automáticamente el escape adecuado según el contexto a todas las variables que se van a generar en la capa de presentación. HTL lo hace posible porque comprende la sintaxis HTML y utiliza ese conocimiento para ajustar el escape necesario para las expresiones, según su posición en el marcado. Esto, por ejemplo, hará que las expresiones colocadas en `href` `src` o los atributos se escapen de forma distinta a las expresiones colocadas en otros atributos o en cualquier otro lugar.

Aunque el mismo resultado se puede lograr con lenguajes de plantilla como JSP, el desarrollador debe asegurarse manualmente de que se aplique el escape adecuado a cada variable. Como una sola omisión o error en el escape aplicado es potencialmente suficiente para causar una vulnerabilidad de scripts entre sitios (XSS), decidimos automatizar esta tarea con HTL. Si es necesario, los desarrolladores pueden especificar un escape diferente en las expresiones, pero con HTL es mucho más probable que el comportamiento predeterminado se corresponda con el comportamiento deseado, lo que reduce la probabilidad de errores.

## Desarrollo simplificado {#simplified-development}

El lenguaje de plantilla HTML es fácil de aprender y sus funciones están limitadas a propósito para garantizar que sea sencillo y directo. También cuenta con poderosos mecanismos para estructurar el marcado y la lógica de invocación, al tiempo que siempre se aplica una estricta separación de preocupaciones entre marcado y lógica. HTL es un HTML5 estándar ya que utiliza expresiones y atributos de datos para anotar el marcado con el comportamiento dinámico deseado, lo que significa que no interrumpe la validez del marcado y lo mantiene legible. Tenga en cuenta que la evaluación de las expresiones y los atributos de datos se realiza completamente en el lado del servidor y no será visible en el lado del cliente, donde se puede utilizar cualquier marco JavaScript deseado sin interferir.

Estas capacidades permiten a los desarrolladores de HTML sin conocimientos de Java y con poco conocimiento específico del producto poder editar las plantillas de HTL, permitiéndoles formar parte del equipo de desarrollo y agilizando la colaboración con los desarrolladores de Java de pila completa. Y viceversa, esto permite a los desarrolladores de Java centrarse en el código back-end sin preocuparse por HTML.

## Costos reducidos {#reduced-costs}

Mayor seguridad, desarrollo simplificado y colaboración en equipo mejorada, se traduce para los proyectos de AEM con menos esfuerzo, tiempo de salida al mercado (TTM) más rápido y menor coste total de propiedad (TCO).

Concretamente, de lo que se ha observado al volver a implementar el sitio Adobe.com con el lenguaje de plantilla HTML se desprende que el coste y la duración del proyecto podrían reducirse en un 25 %.

![](assets/chlimage_1.png)

El diagrama anterior muestra las siguientes mejoras en la eficiencia que podría hacer HTL:

* **** HTML / CSS / JS: Dado que los desarrolladores de HTML pueden editar directamente plantillas HTML, los diseños de front-end ya no tienen que implementarse por separado del proyecto de AEM, sino que pueden implementarse directamente en los componentes de AEM reales. Esto reduce las dolorosas iteraciones con los desarrolladores de Java de pila completa.
* **** JSP / HTL: Dado que HTL en sí no requiere ningún conocimiento de Java y es directo de escribir, cualquier desarrollador con experiencia en HTML está facultado para editar las plantillas.
* **** Java: Gracias a la clara y sencilla utilización de Use-API proporcionada por HTL, se aclara la interfaz con la lógica empresarial, lo que también beneficia el desarrollo de Java en general.

**Lea lo siguiente:**

* [Introducción al lenguaje de plantillas HTML](getting-started.md)

