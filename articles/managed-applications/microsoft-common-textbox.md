---
title: Element interfejsu użytkownika Azure TextBox | Microsoft Docs
description: Opisuje element UI Microsoft. Common. TextBox dla Azure Portal. Służy do dodawania niesformatowanego tekstu.
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
ms.openlocfilehash: b72bfcf06f4c7e256dd227e4edf781c4500c7b19
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331603"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. TextBox — element interfejsu użytkownika
Kontrolka, która może służyć do edycji niesformatowanego tekstu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika
![Microsoft. Common. TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

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
- Jeśli `constraints.required` jest ustawiona na **wartość true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **false**.
- `constraints.regex` jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage` to ciąg, który będzie wyświetlany, gdy wartość pola tekstowego nie powiedzie się. Jeśli nie zostanie określony, będą używane wbudowane komunikaty weryfikacyjne pola tekstowego. Wartość domyślna to **null**.
- Możliwe jest określenie wartości `constraints.regex`, gdy `constraints.required` ma wartość **false**. W tym scenariuszu wartość nie jest wymagana, aby pole tekstowe zostało pomyślnie zweryfikowane. Jeśli jest określony, musi być zgodny ze wzorcem wyrażenia regularnego.

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"my text value"
```

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
