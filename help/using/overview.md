---
title: Información general sobre HTL de AEM
description: El objetivo de HTL, que cuenta con el apoyo de AEM, es ofrecer un marco web empresarial completamente productivo que aumente la seguridad y permita a los desarrolladores de HTML sin conocimientos de Java participar fácilmente en los proyectos de AEM.
translation-type: tm+mt
source-git-commit: c7fa6014cd954a2ccb175e4c3a6be9deb83af890
workflow-type: tm+mt
source-wordcount: '665'
ht-degree: 94%

---


# Información general {#overview}

El objetivo del lenguaje de plantilla HTML (HTL) que es compatible con Adobe Experience Manager (AEM), es ofrecer un marco web empresarial totalmente productivo que aumente la seguridad y permita a los desarrolladores de HTML sin conocimientos de Java participar fácilmente en los proyectos de AEM.

El lenguaje de plantilla HTML es el sistema de plantillas preferido y recomendado del lado del servidor para HTML en AEM. HTL se introdujo con AEM 6.0 y reemplaza a JSP (JavaServer Pages). Para los desarrolladores web que necesitan crear sitios web empresariales sólidos, el lenguaje de plantillas HTML les permite lograr una mayor seguridad y eficacia en el desarrollo.

## Mayor seguridad {#increased-security}

El lenguaje de plantilla HTML aumenta la seguridad de los sitios que lo utilizan en su implementación, en comparación con JSP y con la mayoría de los sistemas de plantillas, ya que HTL es capaz de aplicar automáticamente el escape adecuado según el contexto a todas las variables que se van a generar en la capa de presentación. HTL lo hace posible porque comprende la sintaxis HTML y utiliza ese conocimiento para ajustar el escape necesario para las expresiones, según su posición en el marcado. Esto, por ejemplo, hará que las expresiones colocadas en los atributos `href` o `src` se escapen de forma distinta a las expresiones colocadas en otros atributos o en cualquier otro lugar.

Aunque se pueda lograr el mismo resultado con lenguajes de plantilla como JSP, el desarrollador debe asegurarse manualmente de que se aplique el escape adecuado a cada variable. Como una sola omisión o error en el escape aplicado es potencialmente suficiente para causar una vulnerabilidad en la ejecución de scripts en varios sitios (XSS), decidimos automatizar esta tarea con HTL. Si es necesario, los desarrolladores pueden especificar un escape diferente en las expresiones, pero si se usa HTL es mucho más probable que el comportamiento predeterminado se corresponda con el comportamiento deseado, lo que reduce la probabilidad de que haya errores.

## Desarrollo simplificado {#simplified-development}

El lenguaje de plantilla HTML es fácil de aprender y sus características están limitadas para garantizar que sea sencillo y directo. También cuenta con poderosos mecanismos para estructurar el marcado y la lógica de invocación, a la vez que aplica una estricta separación de diferencias entre el marcado y la lógica. HTL es un HTML5 estándar, ya que utiliza expresiones y atributos de datos para anotar el marcado con el comportamiento dinámico deseado, lo que significa que no interrumpe la validez del marcado y lo mantiene legible. Tenga en cuenta que la evaluación de las expresiones y los atributos de datos se realiza completamente en el lado del servidor y que no será visible en el lado del cliente, donde se puede utilizar cualquier marco de JavaScript sin que este interfiera en el proceso.

Estas capacidades permiten a los desarrolladores de HTML sin conocimientos de Java y con pocos conocimientos del producto específico editar las plantillas HTL, permitiéndoles ser parte del equipo de desarrollo y simplificar la colaboración con todos los desarrolladores de Java. Asimismo, esto también permite a los desarrolladores de Java centrarse en el código final sin tener preocuparse por el HTML.

## Gastos reducidos {#reduced-costs}

Una mayor seguridad, un desarrollo simplificado y un equipo de colaboración mejorado significa menos esfuerzo en los proyectos de AEM, un tiempo de comercialización más rápido (TTM) y un menor costo total de propiedad (TCO).

En concreto, lo que se ha observado al volver a implementar el sitio Adobe.com con el lenguaje de plantilla HTML es que se pueden reducir el costo y la duración del proyecto en un 25 %.

![Aumento y reducción de costos efectivos](assets/chlimage_1.png)

El diagrama que tiene a continuación muestra las posibles mejoras de rendimiento que se pueden conseguir gracias a HTL:

* **HTML / CSS / JS:** dado que los desarrolladores de HTML pueden editar directamente las plantillas de HTL, los diseños de front-end ya no tienen que implementarse aparte del proyecto de AEM, sino que pueden implementarse directamente en los componentes de AEM. Esto reduce las constantes iteraciones que se producen con todos los desarrolladores de Java.
* **JSP / HTL:** como HTL no requiere ningún conocimiento de Java y es simple de escribir, cualquier desarrollador que tenga experiencia con HTML puede editar las plantillas.
* **Java:** gracias al simple uso de Use-API que proporciona HTL, la interfaz con lógica empresarial es más clara, lo que beneficia también a los desarrolladores de Java en general.

**Consulte lo siguiente:**

* [Introducción al lenguaje de plantilla HTML](getting-started.md)
