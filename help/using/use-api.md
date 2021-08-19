---
title: API de uso de HTL
description: 'Hay dos API disponibles para HTL: API de uso de Java y API de uso de Javascript'
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '183'
ht-degree: 100%

---

# API de uso de HTL {#htl-use-api}

HTL promueve la separación de preocupaciones al no permitir que la lógica empresarial se mezcle con el marcado. La lógica empresarial se puede implementar mediante Use-API.

La siguiente tabla ofrece una descripción general de las ventajas y desventajas de cada API.

|  | **API de uso de Java** | **API de uso de JavaScript** |
|--- |--- |--- |
| **Ventajas** | <ul><li>Más rápida</li><li>Se puede inspeccionar con un depurador</li><li>Prueba unitaria fácil de realizar</li></ul> | <ul><li>Los desarrolladores de front-end pueden modificarla</li><li>Se encuentra dentro del componente, para mantener la lógica de vista de un componente cerca de su plantilla correspondiente</li></ul> |
| **Desventajas** | <ul><li>Los desarrolladores de front-end no pueden modificarla</li></ul> | <ul><li>Más lenta</li><li>Sin depurador (aún)</li><li>Prueba unitaria más difícil</li></ul> |

En el caso de los componentes de página, se recomienda utilizar un modelo mixto, donde toda la lógica del modelo se encuentra en Java, lo que proporciona API claras que no son adecuadas para nada de lo que sucede en la vista (es decir, dentro de los componentes). AEM incluye buenos modelos predeterminados como la página o la API de recursos que deberían poder cubrir la mayoría de los casos.

Toda la lógica de vista específica de un componente debe colocarse dentro de ese componente como JavaScript, ya que pertenece a ese componente.
