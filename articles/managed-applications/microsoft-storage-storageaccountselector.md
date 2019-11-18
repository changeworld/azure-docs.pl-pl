---
title: Element interfejsu użytkownika usługi Azure StorageAccountSelector | Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. Storage. StorageAccountSelector dla Azure Portal.
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
ms.openlocfilehash: e1f96b42e58bcb09cfc2836c993626a889669fc0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151456"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI element

Kontrolka służąca do wybierania nowego lub istniejącego konta magazynu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Kontrolka wyświetla wartość domyślną.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Kontrolka umożliwia użytkownikowi utworzenie nowego konta magazynu lub wybranie istniejącego konta magazynu.

![Microsoft. Storage. StorageAccountSelector New](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Uwagi

- Jeśli jest określony, `defaultValue.name` jest automatycznie sprawdzana pod kątem unikatowości. Jeśli nazwa konta magazynu nie jest unikatowa, użytkownik musi określić inną nazwę lub wybrać istniejące konto magazynu.
- Wartość domyślna dla `defaultValue.type` jest **Premium_LRS**.
- Dowolny typ nieokreślony w `constraints.allowedTypes` jest ukryty, a żaden typ nie został określony w `constraints.excludedTypes` jest pokazywany. `constraints.allowedTypes` i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Jeśli `options.hideExisting` ma **wartość true**, użytkownik nie może wybrać istniejącego konta magazynu. Wartość domyślna to **false**.

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
