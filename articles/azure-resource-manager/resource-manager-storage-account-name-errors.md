---
title: Błędy nazwa konta usługi Azure storage | Dokumentacja firmy Microsoft
description: W tym artykule opisano błędy, które można napotkać podczas określania nazwy konta magazynu.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: c3d4d764b1076c8705cfa64d6c0b38e3b8c1a801
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64716392"
---
# <a name="resolve-errors-for-storage-account-names"></a>Rozwiąż problemy dotyczące nazw kont magazynów

W tym artykule opisano błędów nazw, które mogą wystąpić podczas wdrażania konta magazynu.

## <a name="symptom"></a>Objaw

Jeśli nazwa konta magazynu zawiera niedozwolone znaki, występuje błąd, taki jak:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

W przypadku kont magazynu, należy podać nazwę zasobu, która jest unikatowa w obrębie platformy Azure. Jeśli nie podasz unikatową nazwę, pojawi się błąd, taki jak:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Jeśli wdrażanie konta magazynu z taką samą nazwę jak istniejące konto magazynu w ramach subskrypcji, ale Podaj inną lokalizację, otrzymasz komunikat o błędzie informujący, że konto magazynu już istnieje w innej lokalizacji. Usuń istniejące konto magazynu lub podać tej samej lokalizacji co istniejące konto magazynu.

## <a name="cause"></a>Przyczyna

Nazwy kont magazynu musi mieć długość od 3 do 24 znaków długości i użyć tylko cyfry i małe litery. Nazwa musi być unikatowa.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że nazwa konta magazynu jest unikatowa. Można utworzyć unikatowej nazwy przez złączenie konwencji nazewnictwa wynikiem [uniqueString](resource-group-template-functions-string.md#uniquestring) funkcji.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Upewnij się, że nazwę konta magazynu nie może przekraczać 24 znaków. [UniqueString](resource-group-template-functions-string.md#uniquestring) funkcja zwraca 13 znaków. Jeśli łączenie prefiks lub przyrostkowe do **uniqueString** wyniku, podaj wartość, która jest maksymalnie 11 znaków lub mniej.

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

Upewnij się, że nazwa konta magazynu nie zawiera żadnych wielkich liter i znaków specjalnych.