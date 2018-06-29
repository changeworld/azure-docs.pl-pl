---
title: Element PasswordBox interfejsu użytkownika platformy Azure | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Common.PasswordBox interfejsu użytkownika do portalu Azure.
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
ms.openlocfilehash: adaece4a450f758c22ffecb356826962806d9d9b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060107"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI element
Formant, który może służyć do udostępniania i Potwierdź hasło.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
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
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Ten element nie obsługuje `defaultValue` właściwości.
- Aby uzyskać szczegóły implementacji `constraints`, zobacz [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Jeśli `options.hideConfirmation` ustawiono **true**, drugie pole tekstowe potwierdzania hasła jest ukryty. Wartość domyślna to **false**.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
