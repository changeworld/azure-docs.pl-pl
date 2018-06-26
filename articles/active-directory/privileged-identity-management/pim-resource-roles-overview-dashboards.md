---
title: 'Omówienie: Wykonywać Przegląd dostępu w Privileged Identity Management zasobów platformy Azure | Dokumentacja firmy Microsoft'
description: Tym dokumencie opisano, jak przeprowadzić przegląd dostępu w PIM dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b53ae66d927dfacd1048a6bd0fa9abdb6f2a7862
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751156"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Pulpit nawigacyjny zasobów umożliwia wykonywanie przeglądu dostępu

Pulpit nawigacyjny zasobów można użyć do wykonania w zarządzania tożsamości uprzywilejowanych (PIM) Przegląd dostępu dla zasobów platformy Azure. Pulpit nawigacyjny widoku administracyjnej ma trzy główne składniki:

- Graficzna reprezentacja zasobu roli aktywacji.
- Dwa wykresy dystrybucji przypisań ról przez typ przypisania.
- Obszar danych dotyczących nowych przypisań ról.

![Zrzut ekranu przedstawiający Admin widoku pulpitu nawigacyjnego, wyświetlanie wykresów i wykresy](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Zrzut ekranu przedstawiający Admin widoku pulpitu nawigacyjnego, przedstawiający listę danych](media/azure-pim-resource-rbac/role-settings.png)

Graficzna reprezentacja zasobu roli aktywacji obejmuje ostatnich siedmiu dni. Te dane zakresie znajduje się wybrany zasób i wyświetla aktywacji dla najbardziej typowe role (właściciela, współautora, administrator dostępu użytkowników) i dla wszystkich ról połączone.

Po prawej stronie wykresu aktywacji dwa wykresy wyświetlić typ przydziału dla użytkowników i grup dystrybucji przypisań ról. Należy zmienić wartość na wartość procentową (lub odwrotnie), wybierając wycinek wykresu.

Poniżej wykresy zostanie wyświetlony w ciągu ostatnich 30 dni oraz listę ról posortowane według całkowitej przypisania (w kolejności malejącej) liczbę użytkowników i grup z nowych przypisań ról.


