---
title: Wymagania dotyczące licencji dotyczące korzystania z zarządzania tożsamościami uprzywilejowanymi — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Zawiera opis wymagań licencyjnych dotyczących korzystania z usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932318"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Wymagania licencyjne dotyczące korzystania z zarządzania tożsamościami uprzywilejowanymi

Aby korzystać z usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), katalog musi mieć prawidłową licencję. Ponadto licencje muszą być przypisane administratorom i odpowiednim użytkownikom. W tym artykule opisano wymagania dotyczące licencji dotyczące korzystania z zarządzania tożsamościami uprzywilejowanymi.

## <a name="valid-licenses"></a>Ważne licencje

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Ile masz licencji?

Upewnij się, że katalog ma co najmniej tyle licencji usługi Azure AD Premium P2, jak masz pracowników, którzy będą wykonywać następujące zadania:

- Użytkownicy przypisani jako uprawnieni do ról usługi Azure AD zarządzanych przy użyciu usługi PIM
- Użytkownicy mogą zatwierdzać lub odrzucać żądania aktywacji w ułowieniu pim
- Użytkownicy przypisani do roli zasobów platformy Azure z przydziałami tylko w czasie lub bezpośrednio (opartymi na czasie)  
- Użytkownicy przypisani do przeglądu dostępu
- Użytkownicy, którzy wykonują recenzje dostępu

Licencje usługi Azure AD Premium P2 **nie** są wymagane do następujących zadań:

- Żadne licencje nie są wymagane dla użytkowników z rolami administratora globalnego lub administratora ról uprzywilejowanych, które konfigurują usługi PIM, konfigurują zasady, odbierają alerty i konfigurują przeglądy dostępu.

Aby uzyskać więcej informacji o licencjach, zobacz [Przypisywanie lub usuwanie licencji za pomocą portalu usługi Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Przykładowe scenariusze licencji

Oto kilka przykładowych scenariuszy licencji ułatwiające określenie liczby licencji, które muszą mieć.

| Scenariusz | Obliczenia | Liczba licencji |
| --- | --- | --- |
| Woodgrove Bank ma 10 administratorów dla różnych działów i 2 administratorów globalnych, którzy konfigurują i zarządzają pim. Kwalifikują się do tego pięciu administratorów. | Pięć licencji dla administratorów, którzy kwalifikują się | 5 |
| Graphic Design Institute ma 25 administratorów, z których 14 jest zarządzanych za pośrednictwem PIM. Aktywacja roli wymaga zatwierdzenia i trzech różnych użytkowników w organizacji, którzy mogą zatwierdzać aktywacje. | 14 licencji na kwalifikujące się role + trzy osoby zatwierdzające | 17 |
| Contoso ma 50 administratorów, z których 42 są zarządzane za pośrednictwem usługi PIM. Aktywacja roli wymaga zatwierdzenia i istnieje pięciu różnych użytkowników w organizacji, którzy mogą zatwierdzać aktywacje. Contoso wykonuje również comiesięczne przeglądy użytkowników przypisanych do ról administratora, a recenzenci są menedżerami użytkowników, z których sześciu nie ma ról administratora zarządzanych przez pim. | 42 licencje na kwalifikujące się role + pięciu osób zatwierdzających + sześciu recenzentów | 53 |

## <a name="what-happens-when-a-license-expires"></a>Co się stanie po wygaśnięciu licencji?

Jeśli licencja usługi Azure AD Premium P2, EMS E5 lub licencja próbna wygaśnie, funkcje zarządzania tożsamościami uprzywilejowanymi nie będą już dostępne w katalogu:

- Stałe przypisania ról do ról usługi Azure AD pozostaną nienaruszone.
- Usługa zarządzania tożsamościami uprzywilejowanymi w portalu Azure, a także polecenia cmdlet interfejsu Graph api i interfejsy programu PowerShell zarządzania tożsamościami uprzywilejowanymi nie będą już dostępne dla użytkowników, aby aktywować role uprzywilejowane, zarządzać dostępem uprzywilejowanym lub wykonywać dostęp do przeglądów ról uprzywilejowanych.
- Przypisania kwalifikujących się ról usługi Azure AD zostaną usunięte, ponieważ użytkownicy nie będą już mogli aktywować ról uprzywilejowanych.
- Wszystkie bieżące przeglądy dostępu ról usługi Azure AD zostaną kres, a ustawienia konfiguracji usługi Uprzywilejowane zarządzanie tożsamościami zostaną usunięte.
- Zarządzanie tożsamościami uprzywilejowanymi nie będzie już wysyłać wiadomości e-mail po zmianach przypisania roli.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi Privileged Identity Management](pim-deployment-plan.md)
- [Rozpoczęcie korzystania z usługi Privileged Identity Management](pim-getting-started.md)
- [Role, którymi nie można zarządzać w zarządzania tożsamościami uprzywilejowanymi](pim-roles.md)
