---
title: Wyświetlanie raportu dziennika inspekcji dla ról usługi Azure AD w usłudze Azure AD PIM | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyświetlić historię dziennika inspekcji ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329520"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Wyświetlanie historii inspekcji ról usługi Azure AD w dziale Zarządzanie tożsamościami uprzywilejowanymi

Za pomocą historii inspekcji zarządzania tożsamościami uprzywilejowanymi (PIM) można wyświetlić wszystkie przypisania ról i aktywacje w ciągu ostatnich 30 dni dla wszystkich ról uprzywilejowanych. Jeśli chcesz zobaczyć pełną historię inspekcji działań w organizacji usługi Azure Active Directory (Azure AD), w tym administratora, użytkownika końcowego i działania synchronizacji, możesz użyć [raportów zabezpieczeń i działań usługi Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="determine-your-version-of-pim"></a>Określanie wersji programu PIM

Począwszy od listopada 2019 r. część ról usługi Azure AD usługi Zarządzanie tożsamościami uprzywilejowanymi jest aktualizowana do nowej wersji, która odpowiada środowiskom ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmiany w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, które procedury, które należy wykonać w tym artykule, zależą od wersji zarządzania tożsamościami uprzywilejowanymi, które obecnie masz. Wykonaj kroki opisane w tej sekcji, aby ustalić, którą wersję zarządzania tożsamościami uprzywilejowanymi masz. Po poznaniu wersji zarządzania tożsamościami uprzywilejowanymi można wybrać procedury w tym artykule, które są zgodne z tą wersją.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest w roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Jeśli u góry strony przeglądu masz baner, postępuj zgodnie z instrukcjami na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami na karcie **Poprzednia wersja.**

    [![Nowa wersja ról usługi Azure AD](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Wybierz kartę dla swojej wersji")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nowa wersja](#tab/new)

Wykonaj następujące kroki, aby wyświetlić historię inspekcji ról usługi Azure AD.

## <a name="view-resource-audit-history"></a>Wyświetlanie historii inspekcji zasobów

Inspekcja zasobów zapewnia widok wszystkich działań skojarzonych z rolami usługi Azure AD.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **inspekcję zasobów**.

1. Filtruj historię przy użyciu wstępnie zdefiniowanej daty lub zakresu niestandardowego.

    ![Lista inspekcji zasobów z filtrami](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Wyświetlanie audytu

Mój audyt umożliwia wyświetlanie osobistej aktywności roli.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz zasób, dla którego chcesz wyświetlić historię inspekcji.

1. Wybierz **pozycję Mój audyt**.

1. Filtruj historię przy użyciu wstępnie zdefiniowanej daty lub zakresu niestandardowego.

    ![Lista inspekcji dla bieżącego użytkownika](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="view-audit-history"></a>Wyświetlanie historii inspekcji

Wykonaj następujące kroki, aby wyświetlić historię inspekcji ról usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest członkiem roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **historię inspekcji ról katalogów**.

    W zależności od historii inspekcji wyświetlany jest wykres kolumnowy wraz z całkowitą całkowitej liczby aktywacji, maksymalną aktywacją dziennie i średnią aktywacją dziennie.

    [![Nowa wersja ról usługi Azure AD](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Wyświetlanie historii inspekcji ról katalogów")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    U dołu strony wyświetlana jest tabela z informacjami o każdej akcji w dostępnej historii inspekcji. Kolumny mają następujące znaczenie:

    | Kolumna | Opis |
    | --- | --- |
    | Time | Kiedy wystąpiła akcja. |
    | Requestor | Użytkownik, który zażądał aktywacji lub zmiany roli. Jeśli wartością jest **usługa Azure System,** sprawdź historię inspekcji platformy Azure, aby uzyskać więcej informacji. |
    | Akcja | Działania podjęte przez wzywającego. Akcje mogą obejmować Assign, Unassign, Activate, Deactivate lub AddedOutsidePIM. |
    | Członek | Użytkownik, który aktywuje lub jest przypisany do roli. |
    | Rola | Rola przypisana lub aktywowana przez użytkownika. |
    | Rozumowanie | Tekst wprowadzony w polu przyczyna podczas aktywacji. |
    | Wygaśnięcie | Po wygaśnięciu aktywowanej roli. Dotyczy tylko kwalifikujących się przypisań ról. |

1. Aby posortować historię inspekcji, kliknij **przyciski Czas,** **Akcja**i **Rola.**

## <a name="filter-audit-history"></a>Historia inspekcji filtrów

1. U góry strony historii inspekcji kliknij przycisk **Filtruj.**

    Zostanie wyświetlenie okienka **Aktualizuj parametry wykresu.**

1. W **obszarze Zakres czasu**wybierz zakres czasu.

1. W **obszarze Role**zaznacz pola wyboru, aby wskazać role, które chcesz wyświetlić.

    ![Okienko Aktualizowanie parametrów wykresu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Wybierz **pozycję Gotowe,** aby wyświetlić filtrowany dziennik inspekcji.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Uzyskaj przyczynę, osobę zatwierdzaącą i numer biletu dla zdarzeń zatwierdzenia

1. Zaloguj się do [witryny Azure Portal](https://aad.portal.azure.com) przy za pomocą uprawnień administratora roli roli uprzywilejowanej i otwórz usługę Azure AD.
1. Wybierz pozycję **Dzienniki inspekcji**.
1. Użyj filtru **Usługa,** aby wyświetlić tylko zdarzenia inspekcji usługi zarządzania tożsamościami uprzywilejowanymi. Na stronie **Dzienniki inspekcji** można:

    - Zobacz przyczynę zdarzenia inspekcji w kolumnie **Przyczyna stanu.**
    - Zobacz osobę zatwierdzającą w **zainicjowane przez (aktora)** kolumny dla zdarzenia "dodaj członka do żądania roli zatwierdzone".

    [![Nowa wersja ról usługi Azure AD](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrowanie dziennika inspekcji dla usługi PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Wybierz zdarzenie dziennika inspekcji, aby wyświetlić numer biletu na karcie **Aktywność** w okienku **Szczegóły.**
  
    [![Nowa wersja ról usługi Azure AD](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Sprawdź numer biletu dla zdarzenia inspekcji")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Można wyświetlić żądającego (osoba aktywującą rolę) na karcie **Obiekty docelowe** w okienku **Szczegóły** dla zdarzenia inspekcji. Istnieją dwa typy docelowe ról usługi Azure AD:

    - Rola (**Typ** = Rola)
    - Żądający (**Typ** = Użytkownik)

Zazwyczaj zdarzenie dziennika inspekcji bezpośrednio nad zdarzeniem zatwierdzenia jest zdarzeniem "Dodaj element członkowski do roli ukończone", gdzie **zainicjowane przez (aktora)** jest z żądaniem. W większości przypadków nie trzeba znaleźć żądającego w żądaniu zatwierdzenia z punktu widzenia inspekcji.

---

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie historii działań i inspekcji ról zasobów platformy Azure w dziale Zarządzanie tożsamościami uprzywilejowanymi](azure-pim-resource-rbac.md)
