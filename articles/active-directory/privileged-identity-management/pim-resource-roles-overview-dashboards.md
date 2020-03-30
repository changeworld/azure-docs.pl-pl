---
title: Pulpity nawigacyjne zasobów dla przeglądów dostępu w usłudze PIM — Usługa Azure AD | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać pulpitu nawigacyjnego zasobów do wykonywania przeglądu dostępu w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847026"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Wykonywanie przeglądu dostępu w obszarze Zarządzanie tożsamościami uprzywilejowanymi za pomocą pulpitu nawigacyjnego zasobów

Pulpit nawigacyjny zasobów służy do wykonywania przeglądu dostępu w zarządzania tożsamościami uprzywilejowanymi (PIM). Pulpit nawigacyjny widoku administratora w usłudze Azure Active Directory (Azure AD) ma trzy podstawowe składniki:

- Graficzna reprezentacja aktywacji ról zasobów
- Wykresy, na których jest wyświetlany rozkład przypisań ról według typu przydziału
- Obszar danych zawierający informacje o nowych przypisaniach ról

![Zrzut ekranu przedstawiający pulpit nawigacyjny widoku administratora, przedstawiający wykresy i wykresy](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Zrzut ekranu przedstawiający pulpit nawigacyjny widoku administratora z listami danych](media/pim-resource-roles-overview-dashboards/role-settings.png)

Graficzna reprezentacja aktywacji roli zasobów obejmuje ostatnie siedem dni. Te dane są ograniczone do wybranego zasobu i wyświetla aktywacje dla najbardziej typowych ról (właściciel, współautor, administrator dostępu do użytkownika) i dla wszystkich ról razem wziętych.

Po jednej stronie wykresu aktywacji dwa wykresy wyświetlają rozkład przypisań ról według typu przypisania, zarówno dla użytkowników, jak i grup. Można zmienić wartość procentową (lub odwrotnie), wybierając wycinek wykresu.

Poniżej wykresów wymieniono liczbę użytkowników i grup z nowymi przypisaniami ról w ciągu ostatnich 30 dni oraz role posortowane według całkowitych przypisań w kolejności malejącej.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie przeglądu dostępu dla ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-start-access-review.md)
