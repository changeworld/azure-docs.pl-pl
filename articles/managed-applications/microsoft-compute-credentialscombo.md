---
title: Azure elementu interfejsu użytkownika CredentialsCombo | Dokumentacja firmy Microsoft
description: Opis elementu Microsoft.Compute.CredentialsCombo interfejsu użytkownika do portalu Azure.
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
ms.openlocfilehash: 183075f7407b0a0ca6ea53871e239ab8c2d89490
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098624"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI element
Grupa formantów z wbudowanych weryfikacji dla systemu Windows i Linux hasła i klucze publiczne SSH.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika

W systemie Windows będzie widoczna dla użytkowników:

![Microsoft.Compute.CredentialsCombo systemu Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Widoczne dla systemu Linux z hasłem, wybrane:

![Hasło Microsoft.Compute.CredentialsCombo systemu Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Dla systemu Linux z wybrany klucz publiczny SSH Zobacz użytkowników:

![Klucz Microsoft.Compute.CredentialsCombo systemu Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schemat
W systemie Windows należy użyć następującego schematu:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Aby uzyskać **Linux**, użyj następującego schematu:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Uwagi
- `osPlatform` należy określić i mogą być **Windows** lub **Linux**.
- Jeśli `constraints.required` ma ustawioną wartość **true**, a następnie hasło lub pola tekstowego klucza publicznego SSH musi mieć wartości można pomyślnie zweryfikować. Wartość domyślna to **true**.
- Jeśli `options.hideConfirmation` ma ustawioną wartość **true**, a następnie drugie pole tekstowe potwierdzania hasła jest ukryty. Wartość domyślna to **false**.
- Jeśli `options.hidePassword` ustawiono **true**, a następnie opcję uwierzytelniania za pomocą hasła jest ukryty. Można go używać tylko wtedy, gdy `osPlatform` jest **Linux**. Wartość domyślna to **false**.
- Dodatkowe ograniczenia dotyczące dozwolonych haseł można zaimplementować przy użyciu `customPasswordRegex` właściwości. Ciąg w `customValidationMessage` jest wyświetlane, gdy hasło niestandardowego sprawdzania poprawności zakończy się niepowodzeniem. Wartość domyślna dla obu właściwości to **null**.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Jeśli `osPlatform` jest **Windows**, lub `osPlatform` jest **Linux** i użytkownik podał hasła zamiast klucz publiczny SSH, formantu zwraca następujące dane wyjściowe:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Jeśli `osPlatform` jest **Linux** i użytkownika podano klucz publiczny SSH, formantu zwraca następujące dane wyjściowe:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Kolejne kroki
* Aby obejrzeć wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie CreateUiDefinition](create-uidefinition-overview.md).
* Opis właściwości wspólnych elementów interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
