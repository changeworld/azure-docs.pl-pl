---
title: Element interfejsu użytkownika magazynuKonkonselector
description: Zawiera opis elementu interfejsu użytkownika microsoft.storage.storageaccountSelector dla witryny Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651893"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.Storage.StorageKonselector element interfejsu użytkownika

Formant wyboru nowego lub istniejącego konta magazynu.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

Formant pokazuje wartość domyślną.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Formant umożliwia użytkownikowi utworzenie nowego konta magazynu lub wybranie istniejącego konta magazynu.

![Microsoft.Storage.StorageAccountSelector nowy](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

- Jeśli określono, `defaultValue.name` jest automatycznie sprawdzane pod kątem unikatowości. Jeśli nazwa konta magazynu nie jest unikatowa, użytkownik musi określić inną nazwę lub wybrać istniejące konto magazynu.
- Wartością domyślną jest `defaultValue.type` **Premium_LRS**.
- Każdy typ, `constraints.allowedTypes` który nie jest określony w `constraints.excludedTypes` jest ukryty, a każdy typ, który nie jest określony w, jest wyświetlany. `constraints.allowedTypes`i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Jeśli `options.hideExisting` jest **true**, użytkownik nie może wybrać istniejące konto magazynu. Wartość domyślna to **fałsz**.

## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
