---
title: HTL Uso-API
seo-title: Adobe HTL Use-API
description: Hay dos API disponibles para HTL - API para uso de Java y API para uso
  de Javascript
seo-description: Hay dos API disponibles para HTL de Adobe - API para uso de Java
  y API para uso de Javascript
uuid: ab 44 aa 5 c-ce 7 e -40 b 9-97 fb-e 86 c 6 a 28405 c
contentOwner: Usuario
products: SG_ EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: referencia
discoiquuid: 89004426-eb 59-4 b 63-913 f -51 bf 98662773
mwpw-migration-script-version: 2017-10-12 T 21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 5cbaf9c747acf748d12559c2c8e3aba4600cf9a4

---


# HTL Uso-API {#htl-use-api}

La siguiente tabla proporciona información general sobre los inconvenientes y desventajas de cada API.

|  | **Java Use-API** | **API de uso JavaScript** |
|--- |--- |--- |
| **Pros** | <ul><li>más rápido</li><li>se puede inspeccionar con un depurador</li><li>prueba de unidad fácil</li></ul> | <ul><li>los desarrolladores de front-end pueden modificarlos</li><li>se encuentra dentro del componente, manteniendo la lógica de vista de un componente cerca de la plantilla correspondiente</li></ul> |
| **Contras** | <ul><li>los desarrolladores de front-end no pueden modificar</li></ul> | <ul><li>más lento</li><li>sin depurador (aún)</li><li>prueba de unidad más difícil</li></ul> |


Para los componentes de la página, se recomienda utilizar un modelo mixto, donde todas las lógicas del modelo están ubicadas en Java, proporcionando API claras que son indiferentes ante todo lo que ocurre en la vista (es decir, dentro de los componentes). AEM incorpora magníficos modelos predeterminados, como la página o la API de recursos, que deben abarcar la mayoría de los casos.

Toda la lógica de vista específica de un componente debe colocarse dentro de ese componente como JavaScript porque pertenece a dicho componente.
