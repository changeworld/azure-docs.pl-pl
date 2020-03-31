---
title: Element interfejsu użytkownika MultiStorageAccountCombo
description: Zawiera opis elementu interfejsu użytkownika microsoft.storage.MultiStorageAccountCombo dla witryny Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651880"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo Element interfejsu użytkownika

Grupa formantów do tworzenia kilku kont magazynu o nazwach, które zaczynają się od wspólnego prefiksu.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Uwagi

- Wartość dla `defaultValue.prefix` jest łączona z jedną lub kilkoma liczbami całkowitymi w celu wygenerowania sekwencji nazw kont magazynu. Na przykład `defaultValue.prefix` jeśli jest `count` **sa** i jest **2**, a następnie nazwy kont magazynu **sa1** i **sa2** są generowane. Nazwy wygenerowanych kont magazynu są automatycznie sprawdzane pod kątem unikatowości.
- Nazwy kont magazynu są generowane leksykograficznie na `count`podstawie . Na przykład `count` jeśli jest 10, nazwy kont magazynu kończą się dwucyfrowymi liczbami całkowitymi (01, 02, 03).
- Wartość domyślna `defaultValue.prefix` dla to `defaultValue.type` **null**, a dla **Premium_LRS**.
- Każdy typ, `constraints.allowedTypes` który nie jest określony w `constraints.excludedTypes` jest ukryty, a każdy typ, który nie jest określony w, jest wyświetlany. `constraints.allowedTypes`i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Oprócz generowania nazw kont `count` magazynu, służy do ustawiania odpowiedniego mnożnika dla elementu. Obsługuje wartość statyczną, taką jak **2**lub wartość dynamiczną z innego elementu, na przykład. `[steps('step1').storageAccountCount]` Wartość domyślna to **1**.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
