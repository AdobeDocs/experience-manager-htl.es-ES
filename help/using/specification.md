---
title: Especificación de HTL
description: Consulte la especificación de HTL para obtener información detallada sobre la sintaxis.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: tm+mt
source-wordcount: '138'
ht-degree: 73%

---


# Especificación de HTL {#htl-specification}

El lenguaje de plantilla HTML (HTL) es el sistema de plantillas preferido y recomendado del lado del servidor para HTML en AEM.

## Capas HTL {#layers}

AEM Puede definir HTL en mediante una serie de capas.

1. **[Especificación de HTL](https://github.com/adobe/htl-spec)**: HTL es una especificación de código abierto, no basada en plataformas, que cualquiera puede implementar libremente. Sus especificaciones se mantienen en su repositorio de GitHub.
1. **[Motor de script HTL de Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)**: el proyecto Sling ha creado la implementación de referencia de HTL, que utiliza AEM. El proyecto Sling mantiene su documentación.
1. AEM AEM AEM **[Extensiones de](aem-extensions.md)**: se basa en la parte superior del motor de script HTL de Sling para ofrecer a los desarrolladores funciones prácticas específicas de los entornos de trabajo de los que se puede trabajar. Estas extensiones se documentan como parte de este conjunto de documentación.

Siga los vínculos anteriores a la documentación dedicada para todas las capas de HTL utilizadas por AEM.
