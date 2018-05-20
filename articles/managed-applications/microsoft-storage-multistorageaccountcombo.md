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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a4ec5a97f8655c0b5b53dea129d4648a05f6ef85
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element
Grupa służy do tworzenia wielu kont magazynu, których nazwy rozpoczynają się typowe prefiksu.

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
- Wartość `defaultValue.prefix` jest połączony z co najmniej jeden liczb całkowitych na generowanie sekwencji nazw kont magazynu. Na przykład jeśli `defaultValue.prefix` jest **foobar** i `count` jest **2**, następnie nazwy konta magazynu **foobar1** i **foobar2** są generowane. Unikatowość nazw kont magazynu generowane są weryfikowane automatycznie.
- Nazwy konta magazynu są generowane lexicographically na podstawie `count`. Na przykład jeśli `count` to 10, a następnie nazwy konta magazynu kończyć 2-cyfrowe liczby całkowite (01, 02, 03, itp.).
- Wartość domyślna dla `defaultValue.prefix` jest **null**oraz `defaultValue.type` jest **Premium_LRS**.
- Dowolnego typu nie jest określona w `constraints.allowedTypes` jest ukryta i dowolnego typu nie jest określona w `constraints.excludedTypes` jest wyświetlany.
`constraints.allowedTypes` i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
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
