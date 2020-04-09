---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878253"
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

Bieżący zestaw rozszerzeń zainstalowanych przez domyślny pakiet jest wyliczany w tym [pliku extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
