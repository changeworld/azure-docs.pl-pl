---
title: Usługa Azure elementu interfejsu użytkownika z listy rozwijanej | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.DropDown interfejsu użytkownika dla witryny Azure portal.
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
ms.openlocfilehash: e78fa419b067c0bad48229dcfd8d4e986fc16903
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62117305"
---
# <a name="microsoftcommondropdown-ui-element"></a>Element Microsoft.Common.DropDown interfejsu użytkownika
Kontrolka, wybór z listy rozwijanej.

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
- Jeśli zostanie określony, wartość domyślna musi być obecny w etykietę `constraints.allowedValues`. Jeśli nie zostanie określony, pierwszy element `constraints.allowedValues` jest zaznaczone. Wartość domyślna to **null**.
- `constraints.allowedValues` musi mieć co najmniej jeden element.
- Aby emulować wartość nie jest wymagane, Dodaj element z etykiety i wartość `""` (pusty ciąg), aby `constraints.allowedValues`.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"two"
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
