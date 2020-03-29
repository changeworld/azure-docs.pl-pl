---
title: Element rozwijanego interfejsu użytkownika
description: Zawiera opis elementu interfejsu użytkownika microsoft.common.dropdown dla witryny Azure portal. Służy do wybierania spośród dostępnych opcji podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652387"
---
# <a name="microsoftcommondropdown-ui-element"></a>Element interfejsu użytkownika microsoft.common.rozwijany

Kontrolka zaznaczenia z listą rozwijaną.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"two"
```

## <a name="remarks"></a>Uwagi

- Etykieta `constraints.allowedValues` dla jest wyświetlany tekst dla elementu, a jego wartość jest wartością wyjściową elementu, gdy zaznaczona.
- Jeśli jest określony, wartością domyślną `constraints.allowedValues`musi być etykieta obecna w pliku . Jeśli nie zostanie określony, `constraints.allowedValues` zostanie wybrany pierwszy element. Wartość domyślna to **null**.
- `constraints.allowedValues`musi mieć co najmniej jedną pozycję.
- Aby emulować wartość, która nie jest wymagana, dodaj `""` element z etykietą i wartością (pusty ciąg) do . `constraints.allowedValues`

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
