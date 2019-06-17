---
title: Azure elementu interfejsu użytkownika StorageAccountSelector | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Storage.StorageAccountSelector interfejsu użytkownika dla witryny Azure portal.
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
ms.openlocfilehash: c6d4ef50645902aecd57ceb9fc48b7d99bf22d53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104870"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI element
Kontrolka służąca do wybierania konta magazynu do nowego lub istniejącego.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika

Kontrolka pokazuje wartość domyślną.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Kontrolka umożliwia użytkownikowi tworzenie nowego konta magazynu, lub wybierz istniejące konto magazynu.

![Nowe Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Jeśli zostanie określony, `defaultValue.name` jest automatycznie weryfikowana, aby zapewnić unikatowość. Jeśli nazwa konta magazynu nie jest unikatowa, użytkownik musi określić inną nazwę lub wybierz istniejące konto magazynu.
- Wartością domyślną dla `defaultValue.type` jest **Premium_LRS**.
- Dowolny typ nieokreślony w `constraints.allowedTypes` jest ukryta i dowolnego typu, nie jest określona w `constraints.excludedTypes` jest wyświetlany. `constraints.allowedTypes` i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Jeśli `options.hideExisting` jest **true**, użytkownik nie może wybrać istniejące konto magazynu. Wartość domyślna to **false**.

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
