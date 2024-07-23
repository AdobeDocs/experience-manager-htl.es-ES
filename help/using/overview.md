---
title: Información general sobre HTL
description: AEM Descubra cómo admite el lenguaje de plantilla de HTML (HTL) para proporcionar un marco de trabajo web productivo a nivel empresarial que mejore la seguridad. Este marco permite a los desarrolladores HTML AEM sin conocimientos de Java participar mejor en los proyectos de la.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: tm+mt
source-wordcount: '645'
ht-degree: 26%

---


# Información general {#overview}

El lenguaje de plantilla de HTML (HTL), que cuenta con el apoyo de Adobe Experience Manager AEM (), tiene como objetivo proporcionar un marco web empresarial completamente productivo que mejore la seguridad. También permite a los desarrolladores HTML AEM sin conocimientos de Java participar mejor en los proyectos de.

AEM [Presentado en la versión 6.0 ](history.md), el lenguaje de HTML de plantillas es el sistema de plantillas preferido y recomendado en el lado del servidor para el HTML AEM en la. Para los desarrolladores web que necesitan crear sitios web empresariales sólidos, el lenguaje de plantillas HTML les permite lograr una mayor seguridad y eficacia en el desarrollo.

## Mayor seguridad {#increased-security}

El lenguaje de plantilla de HTML (HTL) mejora la seguridad del sitio al aplicar automáticamente el escape según el contexto a todas las variables de salida, lo que lo hace más seguro que la mayoría de los demás sistemas de plantillas. HTL hace posible este enfoque porque comprende la sintaxis del HTML y utiliza ese conocimiento para ajustar el escape necesario para las expresiones, según su posición en el marcado. Este método puede hacer que las expresiones colocadas en los atributos `href` o `src` se escapen de forma distinta a las colocadas en otros atributos o en cualquier otro lugar.

Aunque se pueda lograr el mismo resultado con lenguajes de plantilla como JSP, el desarrollador debe asegurarse manualmente de que se aplique el escape adecuado a cada variable. Como una sola omisión o error en el escape aplicado es potencialmente suficiente para causar una vulnerabilidad en la ejecución de scripts en sitios múltiples (XSS), Adobe decidió automatizar esta tarea con HTL. Si es necesario, los desarrolladores pueden especificar un escape diferente en las expresiones, pero si se usa HTL es mucho más probable que el comportamiento predeterminado se corresponda con el comportamiento deseado, lo que reduce la probabilidad de que haya errores.

## Desarrollo simplificado {#simplified-development}

El lenguaje de plantilla HTML es fácil de aprender y sus características están limitadas para garantizar que sea sencillo y directo. También cuenta con poderosos mecanismos para estructurar el marcado y la lógica de invocación, a la vez que aplica una estricta separación de diferencias entre el marcado y la lógica. HTL es el HTML estándar 5, que utiliza expresiones y atributos de datos para anotar el marcado con comportamiento dinámico. Este método mantiene la validez y legibilidad del marcado. La evaluación de las expresiones y los atributos de datos se realiza completamente en el lado del servidor y no es visible en el lado del cliente, donde se puede utilizar cualquier módulo de JavaScript sin que este interfiera en el proceso.

Estas funcionalidades permiten a los desarrolladores HTML sin conocimientos de Java editar plantillas HTL, integrarse en el equipo de desarrollo y optimizar la colaboración con desarrolladores de Java de pila completa. Y viceversa, permite a los desarrolladores de Java centrarse en el código back-end sin preocuparse por HTML.

## Gastos reducidos {#reduced-costs}

AEM Una mayor seguridad, un desarrollo simplificado y una colaboración en equipo mejorados significa menos esfuerzo en los proyectos de, un tiempo de comercialización más rápido (TTM) y un menor coste total de propiedad (TCO).

La reimplementación del sitio Adobe.com con el lenguaje de plantilla de HTML ha mostrado que los costes y la duración del proyecto se reducen hasta aproximadamente el 25 %.

![Aumento y reducción de costes efectivo](assets/chlimage_1.png)

El diagrama anterior muestra las siguientes mejoras en la eficiencia que HTL podría haber hecho posibles:

* **HTML / CSS / JS:** Los desarrolladores de HTML AEM pueden editar directamente las plantillas de HTL, lo que permite implementar diseños front-end directamente en los componentes de la, eliminando la necesidad de una implementación por separado. Este método reduce las constantes iteraciones que se producen con todos los desarrolladores de Java.
* **JSP / HTL:** Dado que HTL no requiere ningún conocimiento de Java y es simple de escribir, cualquier desarrollador con experiencia en HTML puede editar las plantillas.
* **Java:** gracias al simple uso de Use-API que proporciona HTL, la interfaz con lógica empresarial es más clara, lo que beneficia también a los desarrolladores de Java en general.

## Introducción en vídeo {#video}

AEM El siguiente vídeo de una [sesión de Gems](https://experienceleague.adobe.com/en/docs/events/experience-manager-gems-recordings/gems2014/aem-introduction-to-htl) proporciona información general sobre el propósito de HTL, así como ejemplos de implementación.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Tenga en cuenta que el vídeo hace referencia a HTL por [su nombre anterior, Sightly](history.md).

## Siguientes pasos {#next-steps}

Ahora que conoce los objetivos y ventajas de HTL, puede empezar a utilizar el lenguaje. Consulte [Introducción al lenguaje de HTML](getting-started.md).
