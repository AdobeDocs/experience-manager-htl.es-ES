---
title: Descripción general de AEM HTL
seo-title: Información general sobre la documentación técnica de AEM HTL.
description: El objetivo de HTL, que cuenta con el apoyo de AEM, es ofrecer un marco web empresarial muy productivo que aumente la seguridad y permita a los desarrolladores de HTML sin conocimientos de Java participar mejor en los proyectos de AEM.
seo-description: En este documento se exponen los principios y el propósito del lenguaje de plantilla HTML (HTL) que admite Adobe Experience Manager. HTL es un marco web empresarial altamente productivo que aumenta la seguridad y permite a los desarrolladores de HTML sin conocimientos de Java participar mejor en los proyectos de AEM.
uuid: 8f486325-0a1b-4186-a998-96fc0034c44a
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: introducción
content-type: referencia
discoiquuid: 8f779e08-94c7-43bc-a6e5-d81a9f818c5c
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: 1d4565a4cffa6e5d42d6a5242f7ce62203dc7c63

---


# Información general {#overview}

El objetivo del lenguaje de plantilla HTML (HTL), compatible con Adobe Experience Manager (AEM), es ofrecer un marco web empresarial muy productivo que aumente la seguridad y permita a los desarrolladores de HTML sin conocimientos de Java participar mejor en los proyectos de AEM.

El lenguaje de plantilla HTML se ha introducido con AEM 6.0 y sustituye a JSP (páginas de JavaServer) como sistema de plantilla preferido y recomendado en el servidor para HTML. Para los desarrolladores web que necesitan crear sitios web empresariales sólidos, el lenguaje de plantillas HTML ayuda a lograr una mayor seguridad y eficacia de desarrollo.

## Mayor seguridad {#increased-security}

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

