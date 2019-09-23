---
title: HTL Use-API
seo-title: Adobe HTL Use-API
description: Hay dos API disponibles para HTL - API para uso de Java y API para uso de Javascript
seo-description: Hay dos API disponibles para HTL de Adobe - API para uso de Java y API para uso de Javascript
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c
contentOwner: Usuario
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 89004426-eb59-4b63-913f-51bf98662773
mwpw-migration-script-version: 2017-10-12T21 46 58,665-0400
translation-type: tm+mt
source-git-commit: 5cbaf9c747acf748d12559c2c8e3aba4600cf9a4

---


# HTL Use-API {#htl-use-api}

La siguiente tabla ofrece una visión general de los pros y los contras de cada API.

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **Pros** | <ul><li>más rápido</li><li>se puede inspeccionar con un depurador</li><li>prueba unitaria fácil de realizar</li></ul> | <ul><li>puede ser modificado por desarrolladores de front-end</li><li>se encuentra dentro del componente, manteniendo la lógica de visualización de un componente cerca de la plantilla correspondiente</li></ul> |
| **Cons** | <ul><li>los desarrolladores de front-end no pueden modificar</li></ul> | <ul><li>más lento</li><li>sin depurador (aún)</li><li>prueba de unidad más difícil</li></ul> |


En el caso de los componentes de página, se recomienda utilizar un modelo mixto, donde toda la lógica del modelo se encuentra en Java, proporcionando API claras que sean agnósticos para cualquier cosa que suceda en la vista (es decir, dentro de los componentes). AEM viene con excelentes modelos predeterminados como la página o la API de recursos que deberían poder cubrir la mayoría de los casos.

Toda la lógica de vista específica de un componente debe colocarse dentro de ese componente como JavaScript, ya que pertenece a dicho componente.
