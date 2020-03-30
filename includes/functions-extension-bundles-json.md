---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79382207"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Następujące właściwości są dostępne `extensionBundle`w:

| Właściwość | Opis |
| -------- | ----------- |
| id | Obszar nazw dla pakietów rozszerzeń usług Microsoft Azure Functions. |
| version | Wersja pakietu do zainstalowania. Środowisko wykonawcze Functions zawsze wybiera maksymalną dopuszczalną wersję zdefiniowaną przez zakres wersji lub interwał. Powyższa wartość wersji pozwala na wszystkie wersje pakietów od 1.0.0 do ale nie wliczając 2.0.0. Aby uzyskać więcej informacji, zobacz [notację interwału określającą zakresy wersji](/nuget/reference/package-versioning#version-ranges). |

Wersje pakietu przyrost jako pakiety w pakiecie zmiany. Główne zmiany wersji występują, gdy pakiety w pakiecie przyrost przez wersję główną. Główne zmiany wersji w pakiecie zwykle pokrywają się ze zmianą w głównej wersji środowiska wykonawczego funkcji.  

Bieżący zestaw rozszerzeń zainstalowanych przez domyślny pakiet jest wyliczany w tym [pliku extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
