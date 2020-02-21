---
title: Wyświetlanie raportu inspekcji dla ról usługi Azure AD w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak wyświetlić historię inspekcji dla ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4a2eccc02d13bf5a2dfc8bf3ceb7887e4962489
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498502"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Wyświetlanie historii inspekcji dla ról usługi Azure AD w usłudze PIM

Możesz użyć historii inspekcji Privileged Identity Management (PIM), aby zobaczyć wszystkie przypisania ról i aktywacje w ciągu ostatnich 30 dni dla wszystkich ról uprzywilejowanych. Jeśli chcesz zobaczyć pełną historię inspekcji działania w organizacji usługi Azure Active Directory (Azure AD), w tym administratora, użytkownika końcowego i działania synchronizacji, możesz użyć [raportów zabezpieczeń i działań Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Określanie używanej wersji programu PIM

Od listopada 2019 część Privileged Identity Management ról usługi Azure AD jest aktualizowana do nowej wersji, która jest zgodna z środowiskami dla ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmian w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, procedury, które należy wykonać w tym artykule, zależą od aktualnie posiadanej wersji Privileged Identity Management. Wykonaj kroki opisane w tej sekcji, aby określić, która wersja Privileged Identity Management masz. Po uzyskaniu informacji o wersji Privileged Identity Management możesz wybrać procedury w tym artykule, które pasują do tej wersji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który znajduje się w roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Otwórz **Azure AD Privileged Identity Management**. Jeśli masz baner w górnej części strony przegląd, postępuj zgodnie z instrukcjami wyświetlanymi na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w **poprzedniej wersji** karty.

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nowa wersja](#tab/new)

Wykonaj następujące kroki, aby wyświetlić historię inspekcji dla ról usługi Azure AD.

## <a name="view-resource-audit-history"></a>Wyświetl historię inspekcji zasobów

Inspekcja zasobów umożliwia wyświetlenie wszystkich działań skojarzonych z rolami usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **Inspekcja zasobów**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    ![Lista inspekcji zasobów z filtrami](media/pim-how-to-use-audit-log/resource-audit.png)

## <a name="view-my-audit"></a>Wyświetl moją inspekcję

Moja Inspekcja umożliwia wyświetlenie własnej aktywności roli użytkownika.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz zasób, dla którego ma zostać wyświetlona Historia inspekcji.

1. Wybierz pozycję **Moje inspekcje**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    ![Lista inspekcji dla bieżącego użytkownika](media/pim-how-to-use-audit-log/audit-time-span.png)

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="view-audit-history"></a>Wyświetlanie historii inspekcji

Wykonaj następujące kroki, aby wyświetlić historię inspekcji dla ról usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz opcję **Historia inspekcji ról katalogu**.

    W zależności od historii inspekcji zostanie wyświetlony wykres kolumnowy wraz z łączną liczbą aktywacji, maksymalną liczbą aktywacji dziennie i średnimi aktywacje dziennie.

    ![Historia inspekcji ról katalogu](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    W dolnej części strony zostanie wyświetlona tabela z informacjami o każdej akcji w dostępnej historii inspekcji. Kolumny mają następujące znaczenie:

    | Kolumna | Opis |
    | --- | --- |
    | Time | Gdy wystąpiła akcja. |
    | Obiektu żądającego | Użytkownik, który zażądał aktywacji lub zmiany roli. Jeśli wartość to **Azure system**, sprawdź historię inspekcji platformy Azure, aby uzyskać więcej informacji. |
    | Akcja | Akcje podejmowane przez zleceniodawcę. Akcje mogą obejmować Przypisywanie, cofanie przypisania, aktywowanie, dezaktywowanie lub AddedOutsidePIM. |
    | Element członkowski | Użytkownik, który jest uaktywniany lub przypisany do roli. |
    | Rola | Rola przypisana lub aktywowana przez użytkownika. |
    | Rozsądkiem | Tekst wprowadzony w polu przyczyna podczas aktywacji. |
    | Datę | Po wygaśnięciu aktywowanej roli. Dotyczy tylko kwalifikujących się przypisań ról. |

1. Aby posortować historię inspekcji, kliknij przycisk **godziny**, **akcję**i **rola** .

## <a name="filter-audit-history"></a>Filtruj historię inspekcji

1. W górnej części strony historia inspekcji kliknij przycisk **Filtruj** .

    Zostanie wyświetlone okienko **Aktualizuj parametry wykresu** .

1. W obszarze **zakres czasu**wybierz zakres czasu.

1. W obszarze **role**zaznacz pola wyboru, aby wskazać role, które chcesz wyświetlić.

    ![Okienko aktualizowanie parametrów wykresu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Wybierz pozycję **gotowe** , aby wyświetlić przefiltrowaną historię inspekcji.

---

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie historii działań i inspekcji dla ról zasobów platformy Azure w Privileged Identity Management](azure-pim-resource-rbac.md)
