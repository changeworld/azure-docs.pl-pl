---
title: Usługa Azure elementu interfejsu użytkownika UserNameTextBox | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Compute.UserNameTextBox interfejsu użytkownika dla witryny Azure portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 88ab13329a719ba1e1b8a7b5fba2f7a2d381eca2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251328"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI element
Formant pola tekstowego przy użyciu wbudowanych weryfikacji nazw użytkowników Windows i Linux.

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
- Jeśli `constraints.required` ustawiono **true**, a następnie w polu tekstowym musi mieć wartość do sprawdzania poprawności pomyślnie. Wartość domyślna to **true**.
- `osPlatform` należy określić, i nie może być **Windows** lub **Linux**.
- `constraints.regex` jest to wzorzec wyrażenia regularnego JavaScript. Jeśli zostanie określony, wartość w polu tekstowym musi pasujących do wzorca, aby sprawdzić poprawność pomyślnie. Wartość domyślna to **null**.
- `constraints.validationMessage` jest to ciąg do wyświetlenia, gdy wartość w polu tekstowym nie powiodło się sprawdzanie poprawności określonej przez `constraints.regex`. Jeśli nie zostanie określony, są używane komunikaty wbudowanych sprawdzania poprawności w polu tekstowym. Wartość domyślna to **null**.
- Ten element ma wbudowane sprawdzanie poprawności, która opiera się na wartość określona dla `osPlatform`. Wbudowane sprawdzania poprawności mogą służyć wraz z niestandardowym wyrażeniu regularnym. Jeśli wartość `constraints.regex` jest określony, a następnie sprawdzeń wbudowane i niestandardowe są wyzwalane.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"Example name"
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
