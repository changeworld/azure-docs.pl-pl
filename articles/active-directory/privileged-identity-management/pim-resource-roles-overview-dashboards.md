---
title: 'Omówienie: Wykonywanie przeglądu dostępu w Privileged Identity Management dla zasobów platformy Azure | Dokumentacja firmy Microsoft'
description: W tym dokumencie opisano, jak przeprowadzić przegląd dostępu w usłudze PIM dla zasobów platformy Azure.
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
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9e9053c62f2ead3b6ae7d4ca3c6c38fd1063b8da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441509"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Wykonywanie przeglądu dostępu przy użyciu pulpitu nawigacyjnego zasobu

Zasób pulpit nawigacyjny umożliwia wykonywanie przeglądu dostępu w Privileged Identity Management (PIM) dla zasobów platformy Azure. Administrator Wyświetl pulpit nawigacyjny ma trzy główne składniki:

- Graficzna reprezentacja aktywacji roli zasobu.
- Dwa wykresy, które wyświetlają rozkład przypisań ról, typ przypisania.
- Obszar danych, odnoszące się do nowego przypisania roli.

![Zrzut ekranu pulpitu nawigacyjnego widok administratora i wykresy](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Zrzut ekranu pulpitu nawigacyjnego widok administratora i listę danych](media/azure-pim-resource-rbac/role-settings.png)

Graficzna reprezentacja zasobu aktywacji roli obejmuje ostatnich siedmiu dni. Te dane są ograniczone do wybranego zasobu i wyświetla aktywacji dla najbardziej typowe role (właściciela, współautora, administrator dostępu użytkowników), a także dla wszystkich ról w połączeniu.

Z prawej strony wykresu aktywacji dwa wykresy wyświetlane rozkład przypisań ról według typu przypisania, dla użytkowników i grup. Wartość do wartości procentowej (lub odwrotnie), można zmienić, wybierając wycinek wykresu.

Poniżej wykresów zobaczysz liczbę użytkowników i grup za pomocą nowego przypisania roli w ciągu ostatnich 30 dni oraz listę ról, posortowane według łączna liczba przypisań (w kolejności malejącej).


