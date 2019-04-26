---
title: Azure CredentialsCombo UI element | Microsoft Docs
description: Opis elementu Microsoft.Compute.CredentialsCombo interfejsu użytkownika dla witryny Azure portal.
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
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: 0412d55fe60524cde404e6a640723d3259e020e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251377"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI element
Grupa kontrolek za pomocą wbudowanych sprawdzania poprawności dla Windows i Linux haseł i kluczy publicznych SSH.

## <a name="ui-sample"></a>Przykład interfejsu użytkownika

Dla Windows, będzie widoczna dla użytkowników:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Widoczne dla systemu Linux przy użyciu hasła, wybrane:

![Hasło Microsoft.Compute.CredentialsCombo systemu Linux](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Widoczne dla systemu Linux przy użyciu klucza publicznego SSH, wybrane:

![Microsoft.Compute.CredentialsCombo Linux key](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schemat
Dla Windows użyj następującego schematu:

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
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must contain at least 12 characters, with at least 1 letter and 1 number."
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
- `osPlatform` należy określić, i nie może być **Windows** lub **Linux**.
- Jeśli `constraints.required` ustawiono **true**, wówczas hasła lub pola tekst klucza publicznego SSH musi mieć wartości, aby sprawdzić poprawność pomyślnie. Wartość domyślna to **true**.
- Jeśli `options.hideConfirmation` jest ustawiona na **true**, a następnie w drugim polu tekstowym dla potwierdzenie hasła jest ukryty. Wartość domyślna to **false**.
- Jeśli `options.hidePassword` jest ustawiona na **true**, a następnie opcję, aby użyć uwierzytelniania za pomocą hasła jest ukryty. Mogą być używane tylko wtedy, gdy `osPlatform` jest **Linux**. Wartość domyślna to **false**.
- Dodatkowe ograniczenia dotyczące dozwolonych haseł można zaimplementować przy użyciu `customPasswordRegex` właściwości. Ciąg w `customValidationMessage` jest wyświetlane, gdy hasło niestandardowego sprawdzania poprawności zakończy się niepowodzeniem. Jest wartością domyślną dla obu właściwości **null**.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Jeśli `osPlatform` jest **Windows**, lub `osPlatform` jest **Linux** i użytkownik podał hasła zamiast klucza publicznego SSH, formant zwraca następujące wyniki:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Jeśli `osPlatform` jest **Linux** i użytkownik podał publiczny klucz SSH, formant zwraca następujące wyniki:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Kolejne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika, zobacz [wprowadzenie do zasobu CreateUiDefinition](create-uidefinition-overview.md).
* Aby uzyskać opis wspólne właściwości w elementach interfejsu użytkownika, zobacz [elementy CreateUiDefinition](create-uidefinition-elements.md).
