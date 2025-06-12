---
title: Información general sobre HTL
description: Descubra cómo AEM admite HTL (lenguaje de plantillas HTML) para proporcionar un marco de trabajo web productivo a nivel empresarial que mejora la seguridad. Este marco de trabajo permite a los desarrolladores de HTML que no poseen conocimientos de Java participar de forma productiva en los proyectos AEM.
exl-id: 5d06ff25-d681-4b95-8375-c28a8364eb7e
source-git-commit: 9735ca6ae09ca899c69edd9b63d2e85ad5d6f904
workflow-type: tm+mt
source-wordcount: '677'
ht-degree: 95%

---


# Información general {#overview}

>[!TIP]
>
>**¿Ha considerado Edge Delivery Services para AEM?**
>
>Puede seguir utilizando los métodos descritos en este documento para proyectos existentes. Sin embargo, para nuevos proyectos, Adobe recomienda aprovechar [Edge Delivery Services.](https://experienceleague.adobe.com/es/docs/experience-manager-cloud-service/content/edge-delivery/overview)

El lenguaje de plantillas HTML (HTL) es compatible con Adobe Experience Manager (AEM) y tiene como objetivo proporcionar un marco de trabajo web altamente productivo que mejora la seguridad a nivel empresarial. También permite a los desarrolladores de HTML que no poseen conocimientos de Java participar mejor en los proyectos AEM.

[Introducido en la versión AEM 6.0](history.md), el lenguaje de plantillas HTML es el sistema de plantillas del lado del servidor preferido y recomendado para HTML en AEM. Para los desarrolladores web que necesitan crear sitios web empresariales sólidos, el lenguaje de plantillas HTML les permite lograr una mayor seguridad y eficacia en el desarrollo.

## Mayor seguridad {#increased-security}

El lenguaje de plantillas HTML (HTL) mejora la seguridad del sitio al aplicar automáticamente el escape, según el contexto, a todas las variables de salida, lo que lo hace más seguro que la mayoría de otros sistemas de plantillas. HTL hace posible este enfoque, ya que comprende la sintaxis HTML y utiliza ese conocimiento para ajustar el escape necesario a las expresiones, según su posición en el marcado. Este método puede hacer que las expresiones colocadas en los atributos `href` o `src` se escapen de forma distinta a las expresiones colocadas en otros atributos o en cualquier otro lugar.

Aunque se pueda lograr el mismo resultado con lenguajes de plantillas como JSP, el desarrollador debe asegurarse manualmente de que se aplique el escape adecuado a cada variable. Como una sola omisión o error en el escape aplicado es potencialmente suficiente para causar una vulnerabilidad en la ejecución de scripts en sitios múltiples (XSS), decidimos automatizar esta tarea con HTL. Si es necesario, los desarrolladores pueden especificar un escape diferente en las expresiones, pero si se usa HTL es mucho más probable que el comportamiento predeterminado se corresponda con el comportamiento deseado, lo que reduce la probabilidad de que haya errores.

## Desarrollo simplificado {#simplified-development}

El lenguaje de plantilla HTML es fácil de aprender y sus características están limitadas para garantizar que sea sencillo y directo. También cuenta con poderosos mecanismos para estructurar el marcado y la lógica de invocación, a la vez que aplica una estricta separación de diferencias entre el marcado y la lógica. HTL es HTML5 estándar, que utiliza expresiones y atributos de datos para anotar el marcado con comportamiento dinámico. Este enfoque mantiene la validez y legibilidad del marcado. La evaluación de las expresiones y los atributos de datos se realiza completamente del lado del servidor y no será visible del lado del cliente, donde se puede utilizar cualquier marco de trabajo JavaScript sin que este interfiera en el proceso.

Estas funciones permiten a los desarrolladores de HTML sin conocimientos de Java editar las plantillas HTL, participar en el equipo de desarrollo y optimizar la colaboración con los desarrolladores de Java de pila completa. Asimismo, esto también permite a los desarrolladores de Java centrarse en el código back-end sin tener que preocuparse por el HTML.

## Costes reducidos {#reduced-costs}

Una mayor seguridad, un desarrollo simplificado y una mejor colaboración en equipo significa menos esfuerzo en los Proyectos AEM, un tiempo de salida al mercado (TTM) más rápido y un menor coste total de propiedad (TCO).

La reimplementación del sitio Adobe.com con el lenguaje de las plantillas HTML ha demostrado que los costes y la duración de los proyectos se reducen en hasta aproximadamente un 25 %.

![Aumento y reducción de costes eficaces](assets/chlimage_1.png)

El diagrama anterior muestra las posibles mejoras de rendimiento que se pueden conseguir gracias a HTL:

* **HTML/CSS/JS:** los desarrolladores de HTML pueden editar directamente las plantillas HTL, lo que permite implementar diseños front-end directamente en los componentes de AEM, eliminando la necesidad implementarlos por separado. Este enfoque reduce las constantes iteraciones que se producen con los desarrolladores de Java de pila completa.
* **JSP/HTL:** como HTL no requiere ningún conocimiento de Java y es sencillo de escribir, cualquier desarrollador que tenga experiencia con HTML puede editar las plantillas.
* **Java:** gracias al simple uso de la API de uso de HTL, la interfaz con lógica empresarial es más clara, lo que beneficia también a los desarrolladores de Java en general.

## Introducción en vídeo {#video}

El siguiente vídeo de la [sesión de AEM Gems](https://experienceleague.adobe.com/es/docs/events/experience-manager-gems-recordings/gems2014/aem-introduction-to-htl) ofrece información general sobre el propósito de HTL, así como ejemplos de implementación.

>[!VIDEO](https://video.tv.adobe.com/v/19504/?quality=9)

Tenga en cuenta que el vídeo hace referencia a HTL por [su nombre anterior: Sightly.](history.md)

## Siguientes pasos {#next-steps}

Ahora que conoce los objetivos y las ventajas de HTL, empiece a utilizar el lenguaje. Consulte [Introducción al lenguaje de plantillas HTML](getting-started.md).
