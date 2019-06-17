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
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053935"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Wyświetl historię inspekcji dla ról usługi Azure AD w usłudze PIM

Historia inspekcji usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można użyć, aby zobaczyć wszystkie przypisania ról i aktywacji w ciągu ostatnich 30 dni, dla wszystkich uprzywilejowanych ról. Jeśli chcesz wyświetlić pełną historię inspekcji aktywności w Twoim katalogu, w tym administratora, użytkownik końcowy i działanie synchronizacji, można użyć [raporty dotyczące zabezpieczeń i działania usługi Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Wyświetlanie historii inspekcji

Wykonaj następujące kroki, aby wyświetlić historię inspekcji dla ról usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roli.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **Historia inspekcji ról katalogu**.

    W zależności od Twojej historii inspekcji wykresu kolumnowego jest wyświetlana wraz z łączna liczba aktywacji max uaktywnienia na dzień, a średnie uaktywnienia na dzień.

    ![Historia inspekcji ról katalogu](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    W dolnej części strony tabeli jest wyświetlana przy użyciu informacji o każdej akcji w historii inspekcji dostępne. Kolumny mają następujące znaczenie:

    | Kolumna | Opis |
    | --- | --- |
    | Time | Kiedy wystąpiła Akcja. |
    | Obiekt żądający | Użytkownik, która wnioskowała o aktywacji roli lub Zmień. Jeśli wartość jest **systemu Azure**, można znaleźć w historii inspekcji platformy Azure, aby uzyskać więcej informacji. |
    | Akcja | Akcje wykonywane przez osoby zgłaszającej żądanie. Akcje mogą obejmować Przypisz, Usuń przypisanie, Aktywuj, Dezaktywuj lub AddedOutsidePIM. |
    | Element członkowski | Użytkownik, który jest Aktywacja lub przypisane do roli. |
    | Rola | Rola przypisaniu lub aktywacji przez użytkownika. |
    | Analizowanie | Tekst, który został wprowadzony w pola powód podczas aktywacji. |
    | wygaśnięcie | Kiedy wygaśnie aktywowano rolę. Dotyczy wyłącznie kwalifikujących się przypisań ról. |

1. Aby posortować Historia inspekcji, kliknij **czasu**, **akcji**, i **roli** przycisków.

## <a name="filter-audit-history"></a>Historia inspekcji filtrowania

1. W górnej części strony historii inspekcji kliknij **filtru** przycisku.

    **Aktualizuj parametry wykresu** zostanie wyświetlone okienko.

1. W **zakres czasu**, wybierz zakres czasu.

1. W **role**, Dodaj znaczniki wyboru dla ról, którą chcesz wyświetlić.

    ![Aktualizuj okienko parametry wykresu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Kliknij przycisk **gotowe** wyświetlenia historii inspekcji filtrowane.

## <a name="next-steps"></a>Kolejne kroki

- [Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w usłudze PIM](azure-pim-resource-rbac.md)
