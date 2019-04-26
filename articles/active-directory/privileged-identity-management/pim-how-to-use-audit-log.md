---
title: Wyświetl historię inspekcji dla ról usługi Azure AD w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlać Historia inspekcji dla ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c080173af8ddd31b077bb820ea19d82eb2b29300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440797"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Wyświetl historię inspekcji dla ról usługi Azure AD w usłudze PIM
Historia inspekcji usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można użyć, aby zobaczyć wszystkie przypisania użytkownika i aktywacji w danym okresie czasu dla wszystkich uprzywilejowanych ról. Jeśli chcesz wyświetlić pełną historię inspekcji aktywności w swojej dzierżawie, w tym administratora, użytkownik końcowy i działanie synchronizacji, można użyć [raportów dostępu i użycia usługi Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="navigate-to-audit-history"></a>Przejdź do Historia inspekcji
Z [witryny Azure portal](https://portal.azure.com) pulpitu nawigacyjnego, wybierz opcję **usługi Azure AD Privileged Identity Management** aplikacji. W tym miejscu, dostęp do historii inspekcji, klikając **Zarządzanie rolami uprzywilejowanymi** > **Historia inspekcji** na pulpicie nawigacyjnym usługi PIM.

![Historia inspekcji](media/azure-ad-pim-approval-workflow/image021.png)

> [!NOTE]
> Sortowanie danych według akcji i poszukaj "Aktywacja Approved"


## <a name="audit-history-graph"></a>Wykres historii inspekcji
Historia inspekcji można użyć, aby wyświetlić na wykresie liniowym łączna liczba aktywacji, max uaktywnienia na dzień i średni uaktywnienia na dzień.  Można również filtrować dane według roli, jeśli istnieje więcej niż jednej roli w historii inspekcji.

Użyj **czasu**, **akcji**, i **roli** przycisków, aby posortować historii.

## <a name="audit-history-list"></a>Lista historii inspekcji
Kolumny na liście historii inspekcji są:

* **Obiekt żądający** — użytkownika, która wnioskowała o aktywacji roli lub Zmień.  Jeśli wartość to "Azure System", sprawdź historię inspekcji platformy Azure, aby uzyskać więcej informacji.
* **Użytkownik** — użytkownik, który jest aktywowanie lub są przypisane do roli.
* **Rola** -rolę przypisane lub aktywowane przez użytkownika.
* **Akcja** — akcje wykonywane przez osoby zgłaszającej żądanie. Może to obejmować przypisania, anulowaniu, aktywacji lub dezaktywacji.
* **Czas** — w przypadku wystąpiła Akcja.
* **Uzasadnienie** — Jeśli dowolny tekst został wprowadzony w polu Przyczyna podczas aktywacji, będą wyświetlane tutaj.
* **Wygaśnięcie** — jest to istotne tylko w przypadku aktywacji ról.

## <a name="filter-audit-history"></a>Historia inspekcji filtrowania
Można filtrować informacje, które zostaną wyświetlone w historii inspekcji, klikając **filtru** przycisku.  **Bloku parametry wykresu aktualizacji** będą wyświetlane.

Po ustawieniu filtrów, kliknij przycisk **aktualizacji** do filtrowania danych w historii.  Jeśli dane nie pojawiają się natychmiast, należy odświeżyć stronę.

### <a name="change-the-date-range"></a>Zmień zakres dat
Użyj **już dziś**, **zeszłym tygodniu**, **ostatniego miesiąca**, lub **niestandardowe** przycisków, aby zmienić przedział czasu historii inspekcji.

Po wybraniu **niestandardowe** przycisku, będziesz mieć możliwość wybrania **z** pola daty i **do** pole daty, aby określić zakres dat dla historii.  Możesz wprowadzić daty w formacie MM/DD/RRRR lub kliknąć **kalendarza** ikonę i wybierz datę z kalendarza.

### <a name="change-the-roles-included-in-the-history"></a>Zmień role uwzględnione w historii
Zaznacz lub usuń zaznaczenie pola wyboru **roli** pole wyboru obok każdej roli, aby uwzględnić lub wykluczyć je z historii.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki

- [Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w usłudze PIM](azure-pim-resource-rbac.md)
