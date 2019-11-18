---
title: Element interfejsu użytkownika usługi Azure PasswordBox | Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. Common. PasswordBox dla Azure Portal. Umożliwia użytkownikom podanie wartości klucza tajnego podczas wdrażania aplikacji zarządzanych.
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
ms.openlocfilehash: ab51a4096745c2930199685ac678638a956f21e0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151550"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI element

Kontrolka, która może służyć do udostępniania i potwierdzania hasła.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

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

- Ten element nie obsługuje właściwości `defaultValue`.
- Aby uzyskać szczegółowe informacje dotyczące implementacji `constraints`, zobacz [Microsoft. Common. TextBox](microsoft-common-textbox.md).
- Jeśli `options.hideConfirmation` ma **wartość true**, drugie pole tekstowe potwierdzające hasło użytkownika jest ukryte. Wartość domyślna to **false**.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
