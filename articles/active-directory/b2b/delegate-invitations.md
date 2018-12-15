---
title: Delegowanie zaproszeń do współpracy B2B usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Właściwości użytkownika współpracy w usłudze Azure Active Directory B2B są konfigurowane
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 8abbbba0cdac783dd315cd1f0965e03c4e5a1a05
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434549"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegowanie zaproszeń do współpracy B2B usługi Azure Active Directory

Przy użyciu funkcji współpracy business-to-business (B2B) usługi Azure Active Directory (Azure AD) nie trzeba być administratorem globalnym, aby wysłać zaproszenia. Zamiast tego można użyć zasad i delegowanie zaproszeń do użytkowników, których role zezwolić im na wysyłanie zaproszeń. Jest to ważne, nowy sposób delegować zaproszeniami użytkowników gości za pośrednictwem roli zapraszającego gości.

## <a name="guest-inviter-role"></a>Rola osoba zapraszająca gości
Użytkownika można przypisać do roli osoba zapraszająca gości, aby wysłać zaproszenia. Nie trzeba być członkiem roli administratora globalnego, aby wysłać zaproszenia. Domyślnie użytkownicy również można wywołania interfejsu API zaproszenia, chyba że administrator globalny wyłączona zaproszeń do użytkowników regularne. Użytkownika można także wywoływać interfejs API przy użyciu witryny Azure portal lub programu PowerShell.

Oto przykład, w którym pokazano, jak dodać użytkownika do roli osoby zapraszającej gości za pomocą programu PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Kontrolowanie, kto może zapraszać

W usłudze Azure Active Directory, zaznacz **ustawienia użytkownika**. W obszarze **użytkowników zewnętrznych**, wybierz opcję **Zarządzaj ustawieniami współpracy zewnętrznej**.

> [!NOTE]
> **Ustawieniami współpracy zewnętrznej** są także dostępne z **relacje w organizacji** strony. W usłudze Azure Active Directory w obszarze **Zarządzaj**, przejdź do **relacje w organizacji** > **ustawienia**.

![Ustawienia zewnętrznej współpracy](./media/delegate-invitations/control-who-to-invite.png)

Przy użyciu funkcji współpracy B2B usługi Azure AD administrator dzierżawy można ustawić następujące zasady zaproszenia:

- Wyłącz zaproszenia
- Tylko administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać
- Administratorzy, rola osoba zapraszająca gości i członkowie mogą zapraszać
- Wszyscy użytkownicy, w tym gości, mogą zapraszać

Domyślnie dzierżawy są ustawione na #4. (Wszystkich użytkowników, w tym gości, można zaprosić użytkowników B2B).

## <a name="next-steps"></a>Kolejne kroki

Na współpracy B2B usługi Azure AD, zobacz następujące artykuły:

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Dodawanie użytkowników-gości współpracy B2B bez zaproszenia](add-user-without-invite.md)
- [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)


