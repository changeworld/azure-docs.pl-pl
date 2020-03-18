---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
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

W zbiorze `extensionBundle`są dostępne następujące właściwości:

| Właściwość | Opis |
| -------- | ----------- |
| id | Przestrzeń nazw zbiorów rozszerzeń usługi Microsoft Azure Functions. |
| version | Wersja zbioru do zainstalowania. Środowisko uruchomieniowe usługi Functions zawsze wybiera maksymalnie dozwoloną wersję zdefiniowaną przez zakres wersji lub interwał. Powyższa wartość wersji zezwala na wszystkie wersje zbiorów od 1.0.0 do najbliższej 2.0.0 (ale z jej wykluczeniem). Aby uzyskać więcej informacji, zapoznaj się z [notacją interwału na potrzeby określania zakresów wersji](/nuget/reference/package-versioning#version-ranges). |

Wersje zbiorów zwiększają się wraz z kolejnymi pakietami w ramach zmiany zbioru. Zmiany wersji głównej występują, gdy zwiększana jest wersja główna pakietów w zbiorze. Zmiany wersji głównej zbioru zwykle pokrywają się ze zmianami wersji głównej środowiska uruchomieniowego usługi Functions.  

Bieżący zestaw rozszerzeń zainstalowanych przez zbiór domyślny znajduje się w tym [pliku extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
