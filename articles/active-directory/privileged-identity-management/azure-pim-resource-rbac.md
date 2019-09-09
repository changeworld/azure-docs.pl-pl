---
title: Wyświetlanie historii działań i inspekcji dla ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e1ed018f66d4f5eefd02d587504cf64969f47b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804054"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Wyświetlanie historii działań i inspekcji dla ról zasobów platformy Azure w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można wyświetlać działania, aktywacje i historię inspekcji dla ról zasobów platformy Azure w organizacji. Obejmuje to subskrypcje, grupy zasobów, a nawet maszyny wirtualne. Każdy zasób w Azure Portal, który korzysta z funkcji kontroli dostępu opartej na rolach (RBAC) na platformie Azure, może korzystać z funkcji zarządzania zabezpieczeniami i cyklem życia w programie PIM.

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

Może istnieć wymóg zgodności, w którym należy podać pełną listę przypisań ról do audytorów. Program PIM umożliwia wykonywanie zapytań dotyczących przypisań ról w określonym zasobie, który obejmuje przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej było trudne, aby administratorzy mogli uzyskać pełną listę przypisań ról dla subskrypcji i musiały wyeksportować przypisania ról dla każdego określonego zasobu. Korzystając z usługi PIM, można wysyłać zapytania dotyczące wszystkich aktywnych i kwalifikujących się przypisań ról w ramach subskrypcji, w tym przypisania ról dla wszystkich grup zasobów i zasobów.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Kliknij zasób, dla którego chcesz wyeksportować przypisania ról, takie jak subskrypcja.

1. Kliknij pozycję **Członkowie**.

1. Kliknij przycisk **Eksportuj** , aby otworzyć okienko Eksportuj członkostwo.

    ![Eksportuj okienko członkostwa, aby wyeksportować wszystkie elementy członkowskie](media/azure-pim-resource-rbac/export-membership.png)

1. Kliknij pozycję **Eksportuj wszystkie elementy członkowskie** , aby wyeksportować wszystkie przypisania ról w pliku CSV.

    ![Wyeksportowane przypisania ról w pliku CSV PLI jako wyświetlane w programie Excel](media/azure-pim-resource-rbac/export-csv.png)

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

- [Przypisywanie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
- [Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
- [Wyświetlanie historii inspekcji dla ról usługi Azure AD w usłudze PIM](pim-how-to-use-audit-log.md)
