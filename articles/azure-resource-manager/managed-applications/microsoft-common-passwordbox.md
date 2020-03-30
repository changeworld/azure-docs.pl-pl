---
title: Element interfejsu użytkownika passwordbox
description: Zawiera opis elementu interfejsu użytkownika microsoft.common.passwordbox dla witryny Azure portal. Umożliwia użytkownikom podanie wartości tajnej podczas wdrażania aplikacji zarządzanych.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652309"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Element interfejsu użytkownika microsoft.common.passwordbox

Formant, który może służyć do dostarczania i potwierdzania hasła.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Uwagi

- Ten element nie obsługuje `defaultValue` właściwości.
- Aby uzyskać `constraints`szczegółowe informacje na temat implementacji , zobacz [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Jeśli `options.hideConfirmation` ustawiona jest **wartość true,** drugie pole tekstowe potwierdzające hasło użytkownika jest ukryte. Wartość domyślna to **fałsz**.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
