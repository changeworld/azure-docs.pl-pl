---
title: Azure elementu interfejsu użytkownika UserNameTextBox | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Compute.UserNameTextBox interfejsu użytkownika do portalu Azure.
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: b65b62389fbb66c9461430d1dd2df42e71bf6cff
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI element
Formant pola tekstowego z wbudowanych sprawdzania poprawności nazwy użytkownika systemu Windows i Linux.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schemat
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- Jeśli `constraints.required` ustawiono **true**, a następnie w polu tekstowym musi zawierać wartość do zweryfikowania pomyślnie. Wartość domyślna to **true**.
- `osPlatform` należy określić i mogą być **Windows** lub **Linux**.
- `constraints.regex` jest wzorzec wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi odpowiadać wzorzec można pomyślnie zweryfikować. Wartość domyślna to **null**.
- `constraints.validationMessage` ciąg do wyświetlenia, gdy wartość w polu tekstowym nie powiodło się sprawdzenie poprawności określonej przez `constraints.regex`. Jeśli nie zostanie określony, komunikaty o błędach weryfikacji wbudowane pole tekstowe są używane. Wartość domyślna to **null**.
- Ten element ma wbudowane weryfikacji, który bazuje na wartość określona dla `osPlatform`. Wbudowane sprawdzania poprawności można wraz z niestandardowe wyrażenie regularne.
Jeśli określono wartość `constraints.regex` jest określony, a następnie poprawności wbudowanych i niestandardowych są wyzwalane.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"tabrezm"
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
