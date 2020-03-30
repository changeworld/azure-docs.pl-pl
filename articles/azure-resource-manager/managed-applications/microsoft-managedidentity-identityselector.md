---
title: Element interfejsu użytkownika usługi IdentitySelector
description: W tym artykule opisano element interfejsu użytkownika microsoft.managedidentity.identitySelector dla witryny Azure portal. Służy do przypisywania tożsamości zarządzanych do zasobu.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087546"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector Element interfejsu użytkownika

Formant przypisywania [tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md) dla zasobu we wdrożeniu.

## <a name="ui-sample"></a>Próbka interfejsu użytkownika

Formant składa się z następujących elementów:

![Microsoft.ManagedIdentity.IdentitySelector pierwszy krok](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Gdy użytkownik wybierze **opcję Dodaj,** zostanie otwarty następujący formularz. Użytkownik może wybrać jedną lub więcej tożsamości przypisanych przez użytkownika dla zasobu.

![Microsoft.ManagedIdentity.IdentitySelector drugi krok](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

Wybrane tożsamości są wyświetlane w tabeli. Użytkownik może dodawać lub usuwać elementy z tej tabeli.

![Microsoft.ManagedIdentity.IdentitySelector trzeci krok](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Uwagi

- Użyj **defaultValue.systemAssignedIdentity,** aby ustawić wartość początkową dla kontroli opcji tożsamości przypisanej przez system. Wartością domyślną jest **Wył.** Dozwolone są następujące wartości:
  - **Włączone** — do zasobu jest przypisywana tożsamość przypisana do systemu.
  - **Wyłączone** — tożsamość przypisana do systemu nie jest przypisana do zasobu.
  - **OnOnly** — do zasobu jest przypisywana tożsamość przypisana do systemu. Użytkownicy nie mogą edytować tej wartości podczas wdrażania.
  - **OffOnOnly** — system przypisany tożsamości nie jest przypisany do zasobu. Użytkownicy nie mogą edytować tej wartości podczas wdrażania.

- Jeśli **plik options.hideSystemAssignedIdentity** jest ustawiony na **true,** interfejs użytkownika do skonfigurowania tożsamości przypisanej do systemu nie jest wyświetlany. Wartość domyślna dla tej opcji to **false**.
- Jeśli **options.hideUserAssignedIdentity** jest ustawiona na **true,** interfejs użytkownika, aby skonfigurować tożsamość przypisanego użytkownika nie jest wyświetlany. Zasobu nie jest przypisana tożsamość przypisana użytkownikowi. Wartość domyślna dla tej opcji to **false**.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).
- Aby uzyskać opis wspólnych właściwości w elementach interfejsu użytkownika, zobacz [CreateUiDefinition elementów](create-uidefinition-elements.md).