---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129067"
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

W `extensionBundle`są dostępne następujące właściwości:

| Właściwość | Opis |
| -------- | ----------- |
| id | Przestrzeń nazw dla pakietów rozszerzenia funkcji Microsoft Azure. |
| version | Wersja pakietu do zainstalowania. Środowisko uruchomieniowe funkcji zawsze wybiera maksymalną dopuszczalną wersję zdefiniowaną przez zakres wersji lub interwał. Powyższa wartość wersji zezwala na wszystkie wersje pakietów z 1.0.0 do 2.0.0. Aby uzyskać więcej informacji, zobacz [notacja interwału określania zakresów wersji](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Wersje pakietu zwiększają się wraz z pakietem w ramach zmiany pakietu. Zmiany wersji głównej są wykonywane, gdy pakiety w pakiecie są zwiększane przez wersję główną. Główne zmiany wersji w zbiorze zwykle pokrywają się ze zmianą wersji głównej środowiska uruchomieniowego funkcji.  

Bieżący zestaw rozszerzeń instalowanych przez pakiet domyślny jest wyliczany w tym [pliku Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
