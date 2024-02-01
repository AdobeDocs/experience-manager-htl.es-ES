---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.es-ES
index: y
recommendations: noDisplay
source-git-commit: f891460cc7f247723c3e78031aba385faca6acd7
workflow-type: ht
source-wordcount: '96'
ht-degree: 100%

---


# Metadatos para uso interno

Los metadatos del sistema de creación de GitHub son jerárquicos y se definen con los siguientes niveles crecientes de precedentes.

1. metadata.md
1. TDC
1. Artículo

Los metadatos definidos en el archivo metadata.md se aplican a todo el repositorio, pero se pueden sobrescribir en los niveles de TDC y de artículo. Cualquier anulación de los metadatos debe realizarse en el nivel más bajo posible.

Los metadatos del repositorio experience-manager-core-components.en son los mínimos requeridos.

metadata.md

* `product`
* `git-repo`
* `index: y`

Ya no se utiliza:

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

TDC

* `sub-product`
* `user-guide-title`

Artículo

* `title`
* `description`
* `index: n` (solo para versiones anteriores de componentes)

Encontrará información adicional sobre los metadatos en la [guía de creación interna.](https://experienceleague.adobe.com/docs/authoring-guide-exl/using/authoring/features/metadata.html?lang=es#solution)
