---
title: Especificación de HTL
description: Consulte la especificación de HTL para obtener información detallada sobre la sintaxis.
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '152'
ht-degree: 100%

---


# Especificación de HTL {#htl-specification}

El lenguaje de plantilla HTML (HTL) es el sistema de plantillas preferido y recomendado del lado del servidor para HTML en AEM.

## Capas HTL {#layers}

HTL, como se utiliza en AEM, puede definirse por varias capas.

1. **[Especificación de HTL](https://github.com/adobe/htl-spec)**: HTL es una especificación de código abierto, no basada en plataformas, que cualquiera puede implementar libremente. Sus especificaciones se mantienen en su repositorio de GitHub.
1. **[Motor de script HTL de Sling](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)**: el proyecto Sling ha creado la implementación de referencia de HTL, que utiliza AEM. Su documentación la mantiene el proyecto Sling.
1. **[Extensiones de AEM](aem-extensions.md)**: AEM se basa en el motor de script HTL de Sling para ofrecer funciones prácticas específicas de AEM a los desarrolladores. Estas extensiones se documentan como parte de este conjunto de documentación.

Siga los vínculos anteriores a la documentación dedicada para todas las capas de HTL utilizadas por AEM.
