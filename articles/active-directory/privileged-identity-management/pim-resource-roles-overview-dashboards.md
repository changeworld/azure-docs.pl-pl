---
title: Korzystanie z pulpitu nawigacyjnego zasobów do przeprowadzania przeglądu dostępu w programie PIM-Azure Active Directory | Microsoft Docs
description: Opisuje sposób używania pulpitu nawigacyjnego zasobów do przeprowadzania przeglądu dostępu w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804118"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Korzystanie z pulpitu nawigacyjnego zasobów do przeprowadzania przeglądu dostępu w usłudze PIM

Za pomocą pulpitu nawigacyjnego zasobów można przeprowadzić przegląd dostępu w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Pulpit nawigacyjny widoku administratora ma trzy składniki podstawowe:

- Graficzna reprezentacja aktywacji ról zasobów.
- Dwa wykresy, które wyświetlają rozkład przypisań ról według typu przypisania.
- Obszar danych dotyczący nowych przypisań ról.

![Zrzut ekranu pulpitu nawigacyjnego widoku administratora, przedstawiający wykresy i wykresy](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Zrzut ekranu przedstawiający pulpit nawigacyjny widoku administratora, pokazujący listy danych](media/pim-resource-roles-overview-dashboards/role-settings.png)

Graficzna reprezentacja aktywacji ról zasobów obejmuje ostatnie siedem dni. Te dane są objęte zakresem wybranego zasobu i są wyświetlane aktywacje dla najbardziej typowych ról (właściciel, współautor, administrator dostępu użytkowników) i dla wszystkich połączonych ról.

Po prawej stronie wykresu aktywacji dwa wykresy wyświetlają rozkład przypisań ról według typu przypisania dla użytkowników i grup. Możesz zmienić wartość na procent (lub odwrotnie), wybierając wycinek wykresu.

Poniżej wykresów zobaczysz liczbę użytkowników i grup z nowymi przypisaniami ról w ciągu ostatnich 30 dni oraz listę ról posortowanych według całkowitej liczby przypisań (w kolejności malejącej).

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-start-access-review.md) 
