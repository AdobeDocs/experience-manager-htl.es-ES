---
title: Especificación de HTL
description: Consulte la especificación HTL para obtener información detallada sobre la sintaxis.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: 942c5249cc38e49a7d617ba4cf65a878f2e229ab
workflow-type: tm+mt
source-wordcount: '152'
ht-degree: 6%

---


# Especificación de HTL {#htl-specification}

El lenguaje de plantilla de HTML (HTL) es el sistema de plantillas de servidor recomendado para HTML.

## Capas HTL {#layers}

HTL como se utiliza en AEM puede definirse por varias capas.

1. **[Especificación HTL](https://github.com/adobe/htl-spec)** - HTL es una especificación de código abierto, no basada en plataformas, que cualquiera puede implementar libremente. Sus especificaciones se mantienen en su repositorio de GitHub.
1. **[Motor de secuencias de comandos HTL de Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - El proyecto Sling ha creado la implementación de referencia de HTL, que AEM utiliza. Su documentación es mantenida por el proyecto Sling.
1. **[Extensiones de AEM](aem-extensions.md)** : AEM se basa en el motor de secuencias de comandos HTL de Sling para ofrecer a los desarrolladores funciones prácticas específicas de AEM. Estas extensiones se documentan como parte de este conjunto de documentación.

Siga los enlaces anteriores a la documentación dedicada para todas las capas de HTL utilizadas por AEM.
