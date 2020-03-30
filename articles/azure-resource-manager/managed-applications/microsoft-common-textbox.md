---
title: Element interfejsu użytkownika textbox
description: Zawiera opis elementu interfejsu użytkownika microsoft.common.textbox dla witryny Azure portal. Służy do dodawania niesformatowanego tekstu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652283"
---
# <a name="microsoftcommontextbox-ui-element"></a>Element interfejsu użytkownika microsoft.common.textbox

Formant, który może służyć do edycji niesformatowanego tekstu.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"my text value"
```

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required` ustawiono **wartość true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **fałsz**.
- `constraints.regex`jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage`jest ciągiem do wyświetlenia, gdy wartość pola tekstowego nie powiedzie się sprawdzanie poprawności. Jeśli nie zostanie określony, używane są wbudowane komunikaty sprawdzania poprawności pola tekstowego. Wartość domyślna to **null**.
- Istnieje możliwość określenia wartości, `constraints.regex` gdy `constraints.required` jest ustawiona na **false**. W tym scenariuszu wartość nie jest wymagana dla pola tekstowego, aby pomyślnie sprawdzić poprawność. Jeśli jeden jest określony, musi być zgodny ze wzorcem wyrażenia regularnego.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
