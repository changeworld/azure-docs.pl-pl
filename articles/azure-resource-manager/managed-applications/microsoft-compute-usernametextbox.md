---
title: Element interfejsu użytkownika UserNameTextBox
description: W tym artykule opisano element interfejsu użytkownika microsoft.compute.UserNameTextBox dla witryny Azure portal. Umożliwia użytkownikom dostarczanie nazw użytkowników systemu Windows lub Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651906"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Element interfejsu użytkownika microsoft.compute.userNameTextBox

Kontrolka pola tekstowego z wbudowaną weryfikacją nazw użytkowników systemów Windows i Linux.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

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

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Example name"
```

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required` ustawiono **wartość true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **true**.
- `osPlatform`musi być określony i może być albo **Windows** lub **Linux**.
- `constraints.regex`jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage`jest ciągiem do wyświetlenia, gdy wartość pola `constraints.regex`tekstowego nie powiedzie się sprawdzanie poprawności określone przez program . Jeśli nie zostanie określony, używane są wbudowane komunikaty sprawdzania poprawności pola tekstowego. Wartość domyślna to **null**.
- Ten element ma wbudowaną walidację, która `osPlatform`jest oparta na wartości określonej dla . Wbudowana sprawdzanie poprawności może służyć wraz z niestandardowym wyrażeniem regularnym. Jeśli określono `constraints.regex` wartość dla, a następnie wyzwalane są zarówno wbudowane, jak i niestandardowe sprawdzanie poprawności.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
