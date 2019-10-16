---
title: Element interfejsu użytkownika usługi Azure UserNameTextBox | Microsoft Docs
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. UserNameTextBox dla Azure Portal. Umożliwia użytkownikom dostarczanie nazw użytkowników systemu Windows lub Linux.
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
ms.openlocfilehash: 875c73c546fa52642959e2593d41f9af82c13797
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331593"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft. COMPUTE. UserNameTextBox — element interfejsu użytkownika
Kontrolka pola tekstowego z wbudowaną walidacją nazw użytkowników systemu Windows i Linux.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika
![Microsoft. COMPUTE. UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

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
- Jeśli `constraints.required` jest ustawiona na **wartość true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **true**.
- należy określić `osPlatform` i może to być **system Windows** lub **Linux**.
- `constraints.regex` jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage` to ciąg, który będzie wyświetlany, gdy wartość pola tekstowego nie powiedzie się walidacji określonej przez `constraints.regex`. Jeśli nie zostanie określony, będą używane wbudowane komunikaty weryfikacyjne pola tekstowego. Wartość domyślna to **null**.
- Ten element ma wbudowaną weryfikację opartą na wartości określonej dla `osPlatform`. Wbudowanej walidacji można używać razem z niestandardowym wyrażeniem regularnym. Jeśli wartość `constraints.regex` jest określona, wyzwalane są zarówno wbudowane, jak i niestandardowe walidacji.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
```json
"Example name"
```

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
