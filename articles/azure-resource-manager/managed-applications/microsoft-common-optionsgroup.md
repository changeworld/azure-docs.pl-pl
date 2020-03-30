---
title: Element interfejsu użytkownika grupy options
description: Zawiera opis elementu interfejsu użytkownika grupy Microsoft.Common.OptionsGroup dla witryny Azure portal. Umożliwia użytkownikom wybranie spośród dostępnych opcji podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652348"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup Element interfejsu użytkownika

Kontrolka wyboru z wierszem dostępnych opcji.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
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
- Jeśli jest określony, wartością domyślną `constraints.allowedValues`musi być etykieta obecna w pliku . Jeśli nie zostanie określony, `constraints.allowedValues` pierwszy element w jest zaznaczony domyślnie. Wartość domyślna to **null**.
- `constraints.allowedValues`musi mieć co najmniej jedną pozycję.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
