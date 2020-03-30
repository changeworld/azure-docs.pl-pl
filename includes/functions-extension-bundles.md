---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68639151"
---
Najprostszym sposobem zainstalowania rozszerzeń powiązań jest włączenie [pakietów rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Po włączeniu pakietów automatycznie jest instalowany wstępnie zdefiniowany zestaw pakietów rozszerzeń.

Aby włączyć pakiety rozszerzeń, otwórz plik host.json i zaktualizuj jego zawartość, aby dopasować go do następującego kodu:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```