---
title: Azure elementu interfejsu użytkownika MultiStorageAccountCombo | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Storage.MultiStorageAccountCombo interfejsu użytkownika dla witryny Azure portal.
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
ms.openlocfilehash: 08b65770414e9ee1cb5e478427fe7654b2bb9a78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252450"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element
Grupa formantów do tworzenia kilku kont magazynu, których nazwy rozpoczynają się prefiksem wspólnej.

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
- Wartość `defaultValue.prefix` jest połączona z jedną lub więcej liczb całkowitych na wygenerowanie sekwencji nazw kont magazynu. Na przykład jeśli `defaultValue.prefix` jest **sa** i `count` jest **2**, następnie nazwy kont magazynu **sa1** i **sa2** są generowane. Unikatowość nazwy kont magazynu wygenerowanego są weryfikowane automatycznie.
- Nazwy kont magazynu są generowane leksykograficznie na podstawie `count`. Na przykład jeśli `count` wynosi 10, a następnie nazwy kont magazynu kończyć się znakiem dwóch cyfr całkowitych (01, 02, 03).
- Wartością domyślną dla `defaultValue.prefix` jest **null**oraz `defaultValue.type` jest **Premium_LRS**.
- Dowolny typ nieokreślony w `constraints.allowedTypes` jest ukryta i dowolnego typu, nie jest określona w `constraints.excludedTypes` jest wyświetlany. `constraints.allowedTypes` i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Oprócz generowania nazw kont magazynu, `count` służy do ustawiania odpowiednie mnożnik dla tego elementu. Obsługuje ona wartość statyczną, takie jak **2**, lub wartość dynamiczna z innego elementu, tak jak `[steps('step1').storageAccountCount]`. Wartość domyślna to **1**.

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
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
