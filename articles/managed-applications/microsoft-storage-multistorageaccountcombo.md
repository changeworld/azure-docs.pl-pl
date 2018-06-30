---
title: Azure elementu interfejsu użytkownika MultiStorageAccountCombo | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Storage.MultiStorageAccountCombo interfejsu użytkownika do portalu Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: f5fa81d53e1728e8f566a2a39aed8311828b20c7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108709"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element
Grupa formantów do utworzenia wielu kont magazynu o nazwach rozpoczynających się od prefiksu wspólnej.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Wartość `defaultValue.prefix` jest połączony z co najmniej jeden liczb całkowitych na generowanie sekwencji nazw kont magazynu. Na przykład jeśli `defaultValue.prefix` jest **sa** i `count` jest **2**, następnie nazwy konta magazynu **sa1** i **sa2** są generowane. Unikatowość nazw kont magazynu generowane są weryfikowane automatycznie.
- Nazwy konta magazynu są generowane lexicographically na podstawie `count`. Na przykład jeśli `count` to 10, a następnie nazwy konta magazynu kończyć dwucyfrowe całkowitych (01, 02, 03).
- Wartość domyślna dla `defaultValue.prefix` jest **null**oraz `defaultValue.type` jest **Premium_LRS**.
- Dowolnego typu nie jest określona w `constraints.allowedTypes` jest ukryta i dowolnego typu nie jest określona w `constraints.excludedTypes` jest wyświetlany. `constraints.allowedTypes` i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Oprócz generowania nazw kont magazynu, `count` służy do ustawiania odpowiednich mnożnik dla elementu. Obsługuje ona wartość statyczną, takie jak **2**, lub wartość dynamiczną z innego elementu, tak jak `[steps('step1').storageAccountCount]`. Wartość domyślna to **1**.

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
