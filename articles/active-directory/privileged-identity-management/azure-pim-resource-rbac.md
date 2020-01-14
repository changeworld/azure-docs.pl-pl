---
title: Wyświetlanie raportu inspekcji dla ról zasobów platformy Azure w usłudze PIM — Azure AD | Microsoft Docs
description: Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 905acd206ba574e092f41707c9a5625bcaed7f8d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932373"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Wyświetlanie historii działań i inspekcji dla ról zasobów platformy Azure w Privileged Identity Management

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można wyświetlać działania, aktywacje i historię inspekcji dla ról zasobów platformy Azure w organizacji. Obejmuje to subskrypcje, grupy zasobów, a nawet maszyny wirtualne. Każdy zasób w ramach Azure Portal, który korzysta z funkcji kontroli dostępu opartej na rolach (RBAC) na platformie Azure, może korzystać z możliwości zarządzania zabezpieczeniami i cyklem życia w programie Privileged Identity Management.

> [!NOTE]
> Jeśli organizacja ma funkcje zarządzania, które są używane przez usługę zarządzania [zasobami delegowanymi przez platformę Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), w tym miejscu nie będą wyświetlane przypisania ról autoryzowane przez tego dostawcę usług.

## <a name="view-activity-and-activations"></a>Wyświetl aktywność i aktywacje

Aby zobaczyć akcje wykonywane przez określonego użytkownika w różnych zasobach, można wyświetlić aktywność zasobów platformy Azure skojarzoną z danym okresem aktywacji.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Kliknij zasób, dla którego chcesz wyświetlić aktywność i aktywacje.

1. Kliknij pozycję **role** lub **Członkowie**.

1. Kliknij użytkownika.

    Zobaczysz graficzny widok akcji użytkownika w zasobach platformy Azure według daty. Pokazuje także ostatnie aktywacje ról w tym samym okresie czasu.

    ![Szczegóły użytkownika z podsumowaniem aktywności zasobów i aktywacjami ról](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Kliknij aktywację określonej roli, aby zobaczyć szczegóły i odpowiednie działanie zasobów platformy Azure, które wystąpiło, gdy ten użytkownik był aktywny.

    ![Wybrana aktywacja roli i szczegóły działania wyświetlane według daty](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Eksportowanie przypisań ról z elementami podrzędnymi

Może istnieć wymóg zgodności, w którym należy podać pełną listę przypisań ról do audytorów. Privileged Identity Management umożliwia wykonywanie zapytań dotyczących przypisań ról w określonym zasobie, które obejmują przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej było trudne, aby administratorzy mogli uzyskać pełną listę przypisań ról dla subskrypcji i musiały wyeksportować przypisania ról dla każdego określonego zasobu. Za pomocą Privileged Identity Management można wysyłać zapytania dotyczące wszystkich aktywnych i kwalifikujących się przypisań ról w ramach subskrypcji, łącznie z przypisaniami ról dla wszystkich grup zasobów i zasobów.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Kliknij zasób, dla którego chcesz wyeksportować przypisania ról, takie jak subskrypcja.

1. Kliknij pozycję **Członkowie**.

1. Kliknij przycisk **Eksportuj** , aby otworzyć okienko Eksportuj członkostwo.

    ![Eksportuj okienko członkostwa, aby wyeksportować wszystkie elementy członkowskie](media/azure-pim-resource-rbac/export-membership.png)

1. Kliknij pozycję **Eksportuj wszystkie elementy członkowskie** , aby wyeksportować wszystkie przypisania ról w pliku CSV.

    ![Wyeksportowane przypisania ról w pliku CSV jako wyświetlane w programie Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Wyświetl historię inspekcji zasobów

Inspekcja zasobów umożliwia wyświetlenie wszystkich działań roli dla zasobu.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Kliknij zasób, dla którego chcesz wyświetlić historię inspekcji.

1. Kliknij pozycję **Inspekcja zasobów**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    ![Lista inspekcji zasobów z filtrami](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. W obszarze **Typ inspekcji**wybierz pozycję **Aktywuj (przypisane + aktywowane)** .

    ![Lista inspekcji zasobów filtrowana według typu przeprowadzenia inspekcji](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. W obszarze **Akcja**kliknij pozycję **(działanie)** dla użytkownika, aby zobaczyć szczegóły aktywności tego użytkownika w zasobach platformy Azure.

    ![Szczegóły aktywności użytkownika dla określonej akcji](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Wyświetl moją inspekcję

Moja Inspekcja umożliwia wyświetlenie własnej aktywności roli użytkownika.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Kliknij zasób, dla którego chcesz wyświetlić historię inspekcji.

1. Kliknij pozycję **Moje inspekcje**.

1. Przefiltruj historię przy użyciu wstępnie zdefiniowanej daty lub niestandardowego zakresu.

    ![Lista inspekcji dla bieżącego użytkownika](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Wyświetlanie historii inspekcji dla ról usługi Azure AD w Privileged Identity Management](pim-how-to-use-audit-log.md)
