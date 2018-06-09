---
title: Jak korzystać z dziennika inspekcji w usłudze Azure AD Privileged Identity Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z dziennika inspekcji w rozszerzeniu usługi Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 05a70874367d578beee1dc605510f9370b6abd42
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35232996"
---
# <a name="using-the-audit-log-in-pim"></a>Korzystanie z dziennika inspekcji w PIM
Dziennik inspekcji zarządzania tożsamości uprzywilejowanych (PIM) umożliwia zobaczyć wszystkie przypisania użytkowników i aktywacji w danym okresie. Jeśli chcesz wyświetlić historię inspekcji pełnego działania w dzierżawie, w tym administratora, użytkownika i działania synchronizacji, można użyć [raportów dotyczących dostępu i użycia usługi Azure Active Directory.](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>Przejdź do dziennika inspekcji
Z [portalu Azure](https://portal.azure.com) pulpitu nawigacyjnego, wybierz opcję **Azure AD Privileged Identity Management** aplikacji. Z tego miejsca, dostępu do dziennika inspekcji, klikając **Zarządzanie ról uprzywilejowanych** > **Historia inspekcji** na pulpicie nawigacyjnym usługi PIM.

## <a name="the-audit-log-graph"></a>Wykres dziennika inspekcji
Dziennik inspekcji służy do wyświetlania łączna liczba aktywacji, max aktywacji dziennie i średnia aktywacji dziennie w wykres liniowy.  Można również filtrować dane według roli, jeśli istnieje więcej niż jednej roli w Historia inspekcji.

Użyj **czasu**, **akcji**, i **roli** przycisków, aby posortować dziennika.

## <a name="the-audit-log-list"></a>Lista dziennika inspekcji
Kolumny na liście dziennika inspekcji są:

* **Obiekt żądający** — użytkownik, który jest wymagane uaktywnienie roli lub zmiany.  Jeśli wartość to "Azure System", sprawdź dziennik inspekcji Azure, aby uzyskać więcej informacji.
* **Użytkownik** — użytkownik, który jest aktywowanie lub są przypisane do roli.
* **Rola** -przypisane do roli, lub aktywowany przez użytkownika.
* **Akcja** — akcje wykonywane przez obiekt żądający. Może to obejmować przypisania, anulowaniu, aktywacji lub dezaktywacji.
* **Czas** — gdy akcja wystąpił.
* **Uzasadnienie** — Jeśli dowolny tekst został wprowadzony w polu Przyczyna podczas aktywacji, pojawi się tutaj.
* **Wygaśnięcie** — tylko istotne dla aktywacji ról.

## <a name="filter-the-audit-log"></a>Filtrowanie dziennika inspekcji
Można filtrować dane zostaną wyświetlone w dzienniku inspekcji, klikając **filtru** przycisku.  **Bloku parametry wykresu aktualizacji** będą wyświetlane.

Po ustawieniu filtrów, kliknij przycisk **aktualizacji** do filtrowania danych w dzienniku.  Jeśli dane nie są wyświetlane od razu, Odśwież stronę.

### <a name="change-the-date-range"></a>Zmień zakres dat
Użyj **dzisiaj**, **zeszłym tygodniu**, **ostatnim miesiącu**, lub **niestandardowy** przycisk, aby zmienić zakres czasu dziennika inspekcji.

Po wybraniu **niestandardowy** przycisku, będziesz mieć możliwość **z** Data i **do** pole daty, aby określić zakres dat dla dziennika.  Możesz wprowadzić daty w formacie MM/DD/RRRR lub kliknięcie **kalendarza** ikonę i wybierz datę z kalendarza.

### <a name="change-the-roles-included-in-the-log"></a>Zmień role uwzględnione w Dzienniku
Zaznaczenie lub usunięcie zaznaczenia **roli** pole wyboru obok każdej roli, aby dołączyć lub wykluczyć go z dziennika.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

