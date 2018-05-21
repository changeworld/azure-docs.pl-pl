---
title: Element TextBox interfejsu użytkownika platformy Azure | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.TextBox interfejsu użytkownika do portalu Azure.
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
ms.openlocfilehash: f11098f4af2e85df16e00ce6dac6e7c4b5b34e2b
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
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
  "label": "Some text box",
  "defaultValue": "my value",
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
- Jeśli `constraints.required` ustawiono **true**, a następnie w polu tekstowym musi zawierać wartość do zweryfikowania pomyślnie. Wartość domyślna to **false**.
- `constraints.regex` jest wzorzec wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi odpowiadać wzorzec można pomyślnie zweryfikować. Wartość domyślna to **null**.
- `constraints.validationMessage` jest to ciąg wyświetlany, gdy wartość w polu tekstowym weryfikacji nie powiedzie się. Jeśli nie zostanie określony, komunikaty o błędach weryfikacji wbudowane pole tekstowe są używane. Wartość domyślna to **null**.
- Można określić wartość dla `constraints.regex` podczas `constraints.required` ustawiono **false**. W tym scenariuszu wartość nie jest wymagane dla pola tekstowego sprawdzić poprawność pomyślnie. Jeśli jest określony, musi być zgodna ze wzorcem wyrażenia regularnego.

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"my value"
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
