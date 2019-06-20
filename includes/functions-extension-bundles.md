---
ms.openlocfilehash: 0b0d2ead4a10d037b09c2a532eb21372ffd0cb82
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183012"
---
Aby odwoływać się do usługi Azure Functions 2.x domyślne powiązania, otwórz *host.json* zawartość pliku i aktualizacji, aby dopasować następujący kod.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```