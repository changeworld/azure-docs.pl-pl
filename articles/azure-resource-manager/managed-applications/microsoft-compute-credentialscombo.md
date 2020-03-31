---
title: CredentialsCombo UI element
description: Zawiera opis elementu interfejsu użytkownika microsoft.compute.credentialsCombo dla witryny Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652140"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsPołącznik interfejsu użytkownika

Grupa formantów z wbudowaną weryfikacją haseł systemu Windows i Linux oraz kluczy publicznych SSH.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

W przypadku systemu Windows użytkownicy zobaczą:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

W przypadku systemu Linux z wybranym hasłem użytkownicy zobaczą:

![Hasło systemu Microsoft.Compute.CredentialsCombo Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

W przypadku systemu Linux z wybranym kluczem publicznym SSH użytkownicy zobaczą:

![Microsoft.Compute.CredentialsCombo Klucz Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schemat

W systemie Windows użyj następującego schematu:

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

W przypadku **systemu Linux**użyj następującego schematu:

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

Jeśli `osPlatform` **Windows**jest windows `osPlatform` , lub **linux** i użytkownik pod warunkiem hasła zamiast klucza publicznego SSH, formant zwraca następujące dane wyjściowe:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Jeśli `osPlatform` jest **Linux** i użytkownik pod warunkiem klucza publicznego SSH, formant zwraca następujące dane wyjściowe:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Uwagi

- `osPlatform`musi być określony i może być albo **Windows** lub **Linux**.
- Jeśli `constraints.required` jest ustawiona na **true**, a następnie hasło lub SSH klucz publiczny pola tekstowe muszą mieć wartości, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **true**.
- Jeśli `options.hideConfirmation` jest ustawiona na **true**, to drugie pole tekstowe do potwierdzenia hasła użytkownika jest ukryte. Wartość domyślna to **fałsz**.
- Jeśli `options.hidePassword` jest ustawiona na **true**, opcja korzystania z uwierzytelniania hasłem jest ukryta. Może być używany `osPlatform` tylko wtedy, gdy jest **Linux**. Wartość domyślna to **fałsz**.
- Dodatkowe ograniczenia dotyczące dozwolonych haseł można zaimplementować przy użyciu `customPasswordRegex` właściwości. Ciąg w `customValidationMessage` jest wyświetlany, gdy hasło nie będzie sprawdzania poprawności niestandardowej. Wartość domyślna dla obu właściwości to **null**.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).
