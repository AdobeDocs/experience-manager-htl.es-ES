---
title: Especificación de HTL
description: Consulte la especificación de HTL para obtener información detallada sobre la sintaxis.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
index: false
source-git-commit: 391c5279f0021dbedaffb0c63e67e037d6c782e1
workflow-type: tm+mt
source-wordcount: '135'
ht-degree: 85%

---


# Especificación de HTL {#htl-specification}

El lenguaje de plantilla HTML (HTL) es el sistema de plantillas preferido y recomendado del lado del servidor para HTML en AEM.

## Capas HTL {#layers}

Puede definir HTL en AEM mediante una serie de capas.

1. **[Especificación de HTL](https://github.com/adobe/htl-spec)**: HTL es una especificación de código abierto, no basada en plataformas, que cualquiera puede implementar libremente. Sus especificaciones se mantienen en su repositorio de GitHub.
1. **[Motor de scripts HTL de Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)**: el proyecto `Sling` ha creado la implementación de referencia de HTL, que utiliza AEM. El proyecto `Sling` mantiene su documentación.
1. **[Extensiones de AEM](https://experienceleague.adobe.com/en/docs/experience-manager-htl/content/aem-extensions)**: AEM se basa en el motor de script HTL `Sling` para ofrecer funciones prácticas específicas de AEM a los desarrolladores. Estas extensiones se documentan como parte de este conjunto de documentación.

Siga los vínculos anteriores a la documentación dedicada para todas las capas de HTL utilizadas por AEM.
