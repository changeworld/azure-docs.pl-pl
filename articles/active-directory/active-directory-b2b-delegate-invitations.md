---
title: Delegowanie zaproszeń do skorzystania z usługi Azure Active Directory B2B współpracy | Dokumentacja firmy Microsoft
description: Właściwości użytkownika współpraca w usłudze Azure Active Directory B2B są konfigurowane
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a4a1303c3992b8c576650859eea65591392b9b20
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929297"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegowanie zaproszeń do skorzystania z usługi Azure Active Directory B2B współpracy

Ze współpracą między firmami (B2B) w usłudze Azure Active Directory (Azure AD) nie trzeba być administratorem globalnym, aby wysłać zaproszenia. Można użyć zasad i delegować zaproszeń do użytkowników, których role zezwolić im na wysyłanie zaproszeń do skorzystania z. Jest ważne nowy sposób, aby delegować zaproszenia użytkownika gościa za pomocą roli zapraszającej gościa.

## <a name="guest-inviter-role"></a>Rola zapraszającej gościa
Użytkownika można przypisać do roli zapraszającej gościa, aby wysłać zaproszenia. Nie trzeba być członkiem roli administratora globalnego Wyślij zaproszenia. Domyślnie normalnych użytkowników można także wywoływać interfejs API zaproszenia, chyba że administrator globalny wyłączone zaproszeń do normalnych użytkowników. Użytkownik może również wywołać interfejsu API przy użyciu portalu Azure lub programu PowerShell.

Oto przykład pokazujący sposób, aby dodać użytkownika do roli zapraszającej gościa za pomocą programu PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Formant, który można zaprosić

![Kontrolowanie sposobu zaprosić](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Współpracy B2B usługi Azure AD administratora dzierżawy. można ustawić następujące zasady zaproszenia:

- Wyłącz zaproszenia
- Tylko administratorzy i użytkownicy w roli zapraszającej gościa można zaprosić
- Zaprosić administratorów, roli zapraszającej Gość i członków
- Wszyscy użytkownicy, w tym gości, można zaprosić

Domyślnie dzierżaw są ustawione na #4. (Wszystkich użytkowników, w tym gości, można zaprosić użytkowników B2B).

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Dodaj gości współpracy B2B bez zaproszenia](active-directory-b2b-add-user-without-invite.md)
- [Dodawanie do roli użytkownika współpracy B2B](active-directory-b2b-add-guest-to-role.md)


