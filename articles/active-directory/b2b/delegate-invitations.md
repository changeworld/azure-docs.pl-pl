---
title: Delegowanie zaproszeń do współpracy B2B usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Właściwości użytkownika współpracy w usłudze Azure Active Directory B2B są konfigurowane
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: cd0ee37643e92ba37a9b14408b85a3437fb40d38
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269313"
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

![externalusers](https://user-images.githubusercontent.com/13383753/45905128-2c47f680-bda4-11e8-955d-6219c67935e0.PNG)

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


