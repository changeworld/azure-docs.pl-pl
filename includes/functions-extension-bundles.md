---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: 8110d0a9d574c6691322df2162ca877b031cbc59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442268"
---
Najprostszym sposobem zainstalowania rozszerzeń powiązania jest umożliwienie [pakiety rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Za pomocą pakietów włączone, zestaw wstępnie zdefiniowanych rozszerzeń pakiety są instalowane automatycznie.

Aby włączyć rozszerzenie pakiety, otwórz *host.json* plików i zaktualizuj jego zawartość, aby dopasować następujący kod:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```