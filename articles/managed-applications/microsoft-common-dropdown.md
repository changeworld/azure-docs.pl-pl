---
title: Azure elementu interfejsu użytkownika z listy rozwijanej | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.DropDown interfejsu użytkownika do portalu Azure.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: c8966c70fba1cbb17d377223ed5348075ebb7adc
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommondropdown-ui-element"></a>Element Microsoft.Common.DropDown interfejsu użytkownika
Formant wyboru z listy rozwijanej.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "my value",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Etykieta dla `constraints.allowedValues` jest wyświetlany tekst dla elementu i jego wartość jest wartością danych wyjściowych w przypadku wybrania elementu.
- Jeśli jest określony, wartość domyślna musi być obecne w etykiecie `constraints.allowedValues`. Jeśli nie zostanie określony, pierwszy element `constraints.allowedValues` jest zaznaczone. Wartość domyślna to **null**.
- `constraints.allowedValues` musi zawierać co najmniej jeden element.
- Ten element nie obsługuje `constraints.required` właściwości. Aby emulować to zachowanie, należy dodać element z etykiety i wartości `""` (pusty ciąg) do `constraints.allowedValues`.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"Bar"
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
