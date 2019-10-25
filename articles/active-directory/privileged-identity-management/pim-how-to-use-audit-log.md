---
title: Wyświetlanie historii inspekcji dla ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak wyświetlić historię inspekcji dla ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa1c5121c723148884584ca3f00522c1c459859
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809002"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Wyświetlanie historii inspekcji dla ról usługi Azure AD w usłudze PIM

Możesz użyć historii inspekcji Privileged Identity Management (PIM), aby zobaczyć wszystkie przypisania ról i aktywacje w ciągu ostatnich 30 dni dla wszystkich ról uprzywilejowanych. Jeśli chcesz zobaczyć pełną historię inspekcji działania w organizacji usługi Azure Active Directory (Azure AD), w tym administratora, użytkownika końcowego i działania synchronizacji, możesz użyć [raportów zabezpieczeń i działań Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Wyświetlanie historii inspekcji

Wykonaj następujące kroki, aby wyświetlić historię inspekcji dla ról usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij kolejno pozycje **katalog role historia Inspekcja**.

    W zależności od historii inspekcji zostanie wyświetlony wykres kolumnowy wraz z łączną liczbą aktywacji, maksymalną liczbą aktywacji dziennie i średnimi aktywacje dziennie.

    ![Historia inspekcji ról katalogu](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    W dolnej części strony zostanie wyświetlona tabela z informacjami o każdej akcji w dostępnej historii inspekcji. Kolumny mają następujące znaczenie:

    | Kolumna | Opis |
    | --- | --- |
    | Czas | Gdy wystąpiła akcja. |
    | Obiektu żądającego | Użytkownik, który zażądał aktywacji lub zmiany roli. Jeśli wartość to **Azure system**, sprawdź historię inspekcji platformy Azure, aby uzyskać więcej informacji. |
    | Działanie | Akcje podejmowane przez zleceniodawcę. Akcje mogą obejmować Przypisywanie, cofanie przypisania, aktywowanie, dezaktywowanie lub AddedOutsidePIM. |
    | Członek | Użytkownik, który jest uaktywniany lub przypisany do roli. |
    | Rola | Rola przypisana lub aktywowana przez użytkownika. |
    | Analizowanie | Tekst wprowadzony w polu przyczyna podczas aktywacji. |
    | datę | Po wygaśnięciu aktywowanej roli. Dotyczy tylko kwalifikujących się przypisań ról. |

1. Aby posortować historię inspekcji, kliknij przycisk **godziny**, **akcję**i **rola** .

## <a name="filter-audit-history"></a>Filtruj historię inspekcji

1. W górnej części strony historia inspekcji kliknij przycisk **Filtruj** .

    Zostanie wyświetlone okienko **Aktualizuj parametry wykresu** .

1. W obszarze **zakres czasu**wybierz zakres czasu.

1. W obszarze **role**zaznacz pola wyboru, aby wskazać role, które chcesz wyświetlić.

    ![Okienko aktualizowanie parametrów wykresu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Kliknij pozycję **gotowe** , aby wyświetlić przefiltrowaną historię inspekcji.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie historii działań i inspekcji dla ról zasobów platformy Azure w Privileged Identity Management](azure-pim-resource-rbac.md)
