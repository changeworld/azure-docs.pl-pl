---
title: Błędy nazwy konta magazynu platformy Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: da7147439bba668c6c614c19d91a22ee78275c69
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357347"
---
# <a name="resolve-errors-for-storage-account-names"></a>Rozwiąż problemy dotyczące nazw kont magazynów

W tym artykule opisano błędów nazw, które można napotkać podczas wdrażania konta magazynu.

## <a name="symptom"></a>Objaw

Jeśli nazwa konta magazynu zawiera niedozwolone znaki, komunikat o błędzie takich jak:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

W przypadku kont magazynu należy podać nazwę zasobu, która jest unikatowa w obrębie platformy Azure. Jeśli nie zapewnia unikatową nazwę, komunikat o błędzie takich jak:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Jeśli wdrażania konto magazynu o tej samej nazwie jak istniejącego konta magazynu w ramach subskrypcji, ale Podaj inną lokalizację, zostanie wyświetlony komunikat o błędzie informujący, że konto magazynu już istnieje w innej lokalizacji. Usuń istniejące konto magazynu lub podaj tej samej lokalizacji co istniejące konto magazynu.

## <a name="cause"></a>Przyczyna

Nazwy konta magazynu musi należeć do zakresu od 3 do 24 znaków i korzystać tylko cyfry i małe litery. Nazwa musi być unikatowa.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że nazwa konta magazynu jest unikatowy. Można utworzyć unikatowej nazwy przez łączenie z wynikiem Konwencja nazewnictwa [uniqueString](resource-group-template-functions-string.md#uniquestring) funkcji.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Upewnij się, że nazwa konta magazynu nie może przekraczać 24 znaków. [UniqueString](resource-group-template-functions-string.md#uniquestring) funkcja zwraca 13 znaków. W przypadku łączenia prefiksu lub przyrostka do **uniqueString** powodować, podaj wartość, która jest 11 znaków lub mniej.

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

Upewnij się, że nazwa konta magazynu nie ma żadnych wielkich liter i znaków specjalnych.