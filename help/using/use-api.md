---
title: HTL Use-API
description: Hay dos API disponibles para HTL - API para uso de Java y API para uso de Javascript
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 8%

---


# HTL Use-API {#htl-use-api}

HTL fomenta la separación de preocupaciones al no permitir que la lógica empresarial se mezcle con el marcado. La lógica empresarial se puede implementar mediante la Use-API.

La siguiente tabla ofrece una visión general de las ventajas y desventajas de cada API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Ventajas** | <ul><li>Más rápido</li><li>Se puede inspeccionar con un depurador</li><li>Prueba unitaria fácil de realizar</li></ul> | <ul><li>Pueden ser modificados por desarrolladores de front-end</li><li>Se encuentra dentro del componente, manteniendo la lógica de vista de un componente cerca de su plantilla correspondiente</li></ul> |
| **Desventajas** | <ul><li>Los desarrolladores de front-end no pueden modificar</li></ul> | <ul><li>Más lento</li><li>Sin depurador (aún)</li><li>Ensayo de unidad más difícil</li></ul> |

En el caso de los componentes de página, se recomienda utilizar un modelo mixto, donde toda la lógica del modelo se encuentra en Java, proporcionando API claras que sean agnósticos para cualquier cosa que suceda en la vista (es decir, dentro de los componentes). AEM viene con buenos modelos predeterminados como la página o la API de recursos que deberían poder cubrir la mayoría de los casos.

Toda la lógica de vista específica de un componente debe colocarse dentro de ese componente como JavaScript, ya que pertenece a dicho componente.
