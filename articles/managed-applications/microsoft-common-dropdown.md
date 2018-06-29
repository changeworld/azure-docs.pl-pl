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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: f953e1dc15e12c37c30a86ebd7536b1126bf18f7
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062004"
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
  "label": "Example drop down",
  "defaultValue": "Value two",
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
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi

- Etykieta dla `constraints.allowedValues` jest wyświetlany tekst dla elementu i jego wartość jest wartością danych wyjściowych w przypadku wybrania elementu.
- Jeśli jest określony, wartość domyślna musi być obecne w etykiecie `constraints.allowedValues`. Jeśli nie zostanie określony, pierwszy element `constraints.allowedValues` jest zaznaczone. Wartość domyślna to **null**.
- `constraints.allowedValues` musi mieć co najmniej jeden element.
- Aby emulować wartość nie jest wymagane, należy dodać element z etykiety i wartości `""` (pusty ciąg) do `constraints.allowedValues`.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"two"
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
