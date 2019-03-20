---
title: Wykonywanie przeglądu dostępu w usłudze PIM — platformy Azure przy użyciu pulpitu nawigacyjnego zasobu | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia zasobów pulpit nawigacyjny umożliwia wykonywanie przeglądu dostępu w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abf15731bd125737e7c18ab45782820a856b38
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012682"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Wykonywanie przeglądu dostępu w usłudze PIM przy użyciu pulpitu nawigacyjnego zasobu

Zasób pulpit nawigacyjny umożliwia wykonywanie przeglądu dostępu w Privileged Identity Management (PIM) dla zasobów platformy Azure. Administrator Wyświetl pulpit nawigacyjny ma trzy główne składniki:

- Graficzna reprezentacja aktywacji roli zasobu.
- Dwa wykresy, które wyświetlają rozkład przypisań ról, typ przypisania.
- Obszar danych, odnoszące się do nowego przypisania roli.

![Zrzut ekranu pulpitu nawigacyjnego widok administratora i wykresy](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Zrzut ekranu pulpitu nawigacyjnego widok administratora i listę danych](media/azure-pim-resource-rbac/role-settings.png)

Graficzna reprezentacja zasobu aktywacji roli obejmuje ostatnich siedmiu dni. Te dane są ograniczone do wybranego zasobu i wyświetla aktywacji dla najbardziej typowe role (właściciela, współautora, administrator dostępu użytkowników), a także dla wszystkich ról w połączeniu.

Z prawej strony wykresu aktywacji dwa wykresy wyświetlane rozkład przypisań ról według typu przypisania, dla użytkowników i grup. Wartość do wartości procentowej (lub odwrotnie), można zmienić, wybierając wycinek wykresu.

Poniżej wykresów zobaczysz liczbę użytkowników i grup za pomocą nowego przypisania roli w ciągu ostatnich 30 dni oraz listę ról, posortowane według łączna liczba przypisań (w kolejności malejącej).

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczynanie przeglądu dostępu dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-start-access-review.md) 
