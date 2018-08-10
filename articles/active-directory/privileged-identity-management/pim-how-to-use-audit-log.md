---
title: Jak korzystać z dziennika inspekcji w usłudze Azure AD Privileged Identity Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z dziennika inspekcji w rozszerzeniu Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 922658f75a0d00998072c7bd8f2160e9db27f318
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627794"
---
# <a name="using-the-audit-log-in-pim"></a>Korzystanie z dziennika inspekcji w usłudze PIM
Korzystanie z dziennika inspekcji Privileged Identity Management (PIM), aby zobaczyć wszystkie przypisania użytkownika i aktywacji w danym okresie czasu. Jeśli chcesz wyświetlić pełną historię inspekcji aktywności w swojej dzierżawie, w tym administratora, użytkownik końcowy i działanie synchronizacji, można użyć [raportów dostępu i użycia usługi Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-the-audit-log"></a>Przejdź do dziennika inspekcji
Z [witryny Azure portal](https://portal.azure.com) pulpitu nawigacyjnego, wybierz opcję **usługi Azure AD Privileged Identity Management** aplikacji. W tym miejscu, dostęp do dziennika inspekcji, klikając **Zarządzanie rolami uprzywilejowanymi** > **Historia inspekcji** na pulpicie nawigacyjnym usługi PIM.

## <a name="the-audit-log-graph"></a>Wykres dziennika inspekcji
Dziennik inspekcji można użyć, aby wyświetlić na wykresie liniowym łączna liczba aktywacji, max uaktywnienia na dzień i średni uaktywnienia na dzień.  Można również filtrować dane według roli, jeśli istnieje więcej niż jednej roli w historii inspekcji.

Użyj **czasu**, **akcji**, i **roli** przycisków, aby posortować w dzienniku.

## <a name="the-audit-log-list"></a>Lista dzienników inspekcji
Kolumny na liście dziennika inspekcji są:

* **Obiekt żądający** — użytkownika, która wnioskowała o aktywacji roli lub Zmień.  Jeśli wartość to "Azure System", sprawdź w dzienniku inspekcji platformy Azure, aby uzyskać więcej informacji.
* **Użytkownik** — użytkownik, który jest aktywowanie lub są przypisane do roli.
* **Rola** -rolę przypisane lub aktywowane przez użytkownika.
* **Akcja** — akcje wykonywane przez osoby zgłaszającej żądanie. Może to obejmować przypisania, anulowaniu, aktywacji lub dezaktywacji.
* **Czas** — w przypadku wystąpiła Akcja.
* **Uzasadnienie** — Jeśli dowolny tekst został wprowadzony w polu Przyczyna podczas aktywacji, będą wyświetlane tutaj.
* **Wygaśnięcie** — jest to istotne tylko w przypadku aktywacji ról.

## <a name="filter-the-audit-log"></a>Filtruj dziennik inspekcji
Można filtrować informacje, które pojawia się w dzienniku inspekcji, klikając **filtru** przycisku.  **Bloku parametry wykresu aktualizacji** będą wyświetlane.

Po ustawieniu filtrów, kliknij przycisk **aktualizacji** do filtrowania danych w dzienniku.  Jeśli dane nie pojawiają się natychmiast, należy odświeżyć stronę.

### <a name="change-the-date-range"></a>Zmień zakres dat
Użyj **już dziś**, **zeszłym tygodniu**, **ostatniego miesiąca**, lub **niestandardowe** przycisków, aby zmienić zakres czasu dziennika inspekcji.

Po wybraniu **niestandardowe** przycisku, będziesz mieć możliwość wybrania **z** pola daty i **do** pole daty, aby określić zakres dat dla dziennika.  Możesz wprowadzić daty w formacie MM/DD/RRRR lub kliknąć **kalendarza** ikonę i wybierz datę z kalendarza.

### <a name="change-the-roles-included-in-the-log"></a>Zmień role uwzględnione w Dzienniku
Zaznacz lub usuń zaznaczenie pola wyboru **roli** pole wyboru obok każdej roli, aby uwzględnić lub wykluczyć je z dziennika.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

