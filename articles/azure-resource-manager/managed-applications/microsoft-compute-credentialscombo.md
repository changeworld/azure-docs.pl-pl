---
title: CredentialsCombo — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. CredentialsCombo dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652140"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI element

Grupa kontrolek z wbudowaną walidacją haseł systemu Windows i Linux oraz kluczy publicznych SSH.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

W przypadku systemu Windows użytkownicy widzą następujące tematy:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Dla systemu Linux z wybranym hasłem użytkownicy zobaczą:

![Hasło Microsoft. COMPUTE. CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Dla systemu Linux z wybranym kluczem publicznym SSH użytkownicy zobaczą:

![Microsoft. COMPUTE. CredentialsCombo — klucz systemu Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schemat

W przypadku systemu Windows należy użyć następującego schematu:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

W przypadku systemu **Linux**należy użyć następującego schematu:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Jeśli `osPlatform` jest **Windows**lub `osPlatform` jest systemem **Linux** , a użytkownik podał hasło zamiast klucza publicznego SSH, kontrolka zwróci następujące dane wyjściowe:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Jeśli `osPlatform` jest system **Linux** , a użytkownik podał klucz publiczny SSH, kontrolka zwróci następujące dane wyjściowe:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Uwagi

- należy określić `osPlatform` i może to być **system Windows** lub **Linux**.
- Jeśli `constraints.required` ma **wartość true**, pola tekstowe hasła lub klucza publicznego SSH muszą mieć wartości, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **true**.
- Jeśli `options.hideConfirmation` ma **wartość true**, drugie pole tekstowe potwierdzające hasło użytkownika jest ukryte. Wartość domyślna to **false**.
- Jeśli `options.hidePassword` ma **wartość true**, opcja używania uwierzytelniania hasła jest ukryta. Może być używana tylko wtedy, gdy `osPlatform` jest **Linux**. Wartość domyślna to **false**.
- Dodatkowe ograniczenia dotyczące dozwolonych haseł można zaimplementować przy użyciu właściwości `customPasswordRegex`. Ciąg w `customValidationMessage` jest wyświetlany w przypadku niepowodzenia niestandardowej walidacji hasła. Wartość domyślna dla obu właściwości jest **równa null**.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
