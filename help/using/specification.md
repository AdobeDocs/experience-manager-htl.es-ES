---
title: Especificación de HTL
description: Consulte la especificación de HTL para obtener información detallada sobre la sintaxis.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '134'
ht-degree: 62%

---


# Especificación de HTL {#htl-specification}

El lenguaje de plantilla HTML (HTL) es el sistema de plantillas preferido y recomendado del lado del servidor para HTML en AEM.

## Capas HTL {#layers}

Puede definir HTL en AEM mediante una serie de capas.

1. **[Especificación de HTL](https://github.com/adobe/htl-spec)**: HTL es una especificación de código abierto, no basada en plataformas, que cualquiera puede implementar libremente. Sus especificaciones se mantienen en su repositorio de GitHub.
1. **[Motor de scripts HTL de Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - El proyecto `Sling` ha creado la implementación de referencia de HTL, que utiliza AEM. El proyecto `Sling` mantiene su documentación.
1. **[Extensiones de AEM](aem-extensions.md)**: AEM se basa en el motor de script HTL `Sling` para ofrecer funciones prácticas específicas de AEM a los desarrolladores. Estas extensiones se documentan como parte de este conjunto de documentación.

Siga los vínculos anteriores a la documentación dedicada para todas las capas de HTL utilizadas por AEM.
