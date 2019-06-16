---
title: Usługa Azure elementu interfejsu użytkownika w polu tekstowym | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.TextBox interfejsu użytkownika dla witryny Azure portal.
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
ms.openlocfilehash: b06e8b49efe8b6de720fa9bb819d4720e4f80fb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044552"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI element
Formant, który może służyć do edycji niesformatowanego tekstu.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Jeśli `constraints.required` ustawiono **true**, a następnie w polu tekstowym musi mieć wartość do sprawdzania poprawności pomyślnie. Wartość domyślna to **false**.
- `constraints.regex` jest to wzorzec wyrażenia regularnego JavaScript. Jeśli zostanie określony, wartość w polu tekstowym musi pasujących do wzorca, aby sprawdzić poprawność pomyślnie. Wartość domyślna to **null**.
- `constraints.validationMessage` jest to ciąg wyświetlany, gdy w polu tekstowym wartość weryfikacji nie powiedzie się. Jeśli nie zostanie określony, są używane komunikaty wbudowanych sprawdzania poprawności w polu tekstowym. Wartość domyślna to **null**.
- Istnieje możliwość określenia wartości `constraints.regex` podczas `constraints.required` ustawiono **false**. W tym scenariuszu wartość nie jest wymagane dla tego pola tekstowego sprawdzić poprawność pomyślnie. Jeśli jest określony, musi być zgodna wzorzec wyrażenia regularnego.

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"my text value"
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
