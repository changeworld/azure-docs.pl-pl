---
title: Błędy nazwy konta magazynu
description: W tym artykule opisano błędy, które mogą wystąpić podczas określania nazwy konta magazynu.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153976"
---
# <a name="resolve-errors-for-storage-account-names"></a>Rozwiązywanie problemów z nazwami kont magazynu

W tym artykule opisano błędy nazewnictwa, które mogą wystąpić podczas wdrażania konta magazynu.

## <a name="symptom"></a>Objaw

Jeśli nazwa konta magazynu zawiera niedozwolone znaki, pojawia się błąd, taki jak:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

W przypadku kont magazynu należy podać nazwę zasobu, który jest unikatowy na platformie Azure. Jeśli nie podasz unikatowej nazwy, zostanie zwrócony błąd podobny do tego:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Jeśli wdrożysz konto magazynu o takiej samej nazwie jak istniejące konto magazynu w subskrypcji, ale podasz inną lokalizację, zostanie wyświetlony błąd wskazujący, że konto magazynu już istnieje w innej lokalizacji. Usuń istniejące konto magazynu lub podaj tę samą lokalizację, co istniejące konto magazynu.

## <a name="cause"></a>Przyczyna

Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i mniejszych liter. Nazwa musi być unikatowa.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że nazwa konta magazynu jest unikatowa. Można utworzyć unikatową nazwę, łącząc konwencję nazewnictwa z wynikiem funkcji [uniqueString.](template-functions-string.md#uniquestring)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Upewnij się, że nazwa konta magazynu nie przekracza 24 znaków. Funkcja [uniqueString](template-functions-string.md#uniquestring) zwraca 13 znaków. Jeśli łączysz prefiks lub postfix z wynikiem **uniqueString,** podaj wartość, która jest 11 znaków lub mniej.

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

Upewnij się, że nazwa konta magazynu nie zawiera wielkich liter ani znaków specjalnych.