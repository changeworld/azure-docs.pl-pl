---
title: Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyświetlanie działań i inspekcji historii dla ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
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
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bd491d992ed15df288d9226b58bfe832e0692a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476498"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Wyświetl historię działań i inspekcji dla ról zasobów platformy Azure w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), mogą wyświetlać działania, aktywacji i Historia inspekcji dla ról zasobów platformy Azure w Twojej organizacji. W tym subskrypcji, grupy zasobów i nawet maszyn wirtualnych. Dowolnego zasobu w witrynie Azure portal, która korzysta z funkcji kontroli dostępu opartej na rolach na platformie Azure korzystać z zalet zabezpieczeń i cyklu życia możliwości zarządzania w usłudze PIM.

## <a name="view-activity-and-activations"></a>Wyświetl działania i aktywacji

Aby zobaczyć, jakie akcje określonego użytkownika odbyło się w różnych zasobów, możesz wyświetlić działania zasobów platformy Azure, który jest skojarzony z okresem danego aktywacji.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Kliknij zasób, którą chcesz wyświetlić, działania i aktywacji dla.

1. Kliknij przycisk **role** lub **członków**.

1. Kliknij użytkownika.

    Zobaczysz graficzne przedstawienie czynności wykonywanych przez użytkownika w ramach zasobów platformy Azure według daty. Pokazuje również ostatnie aktywacje ról w tym samym okresie czasu.

    ![Szczegóły użytkownika za pomocą zasobów działania Podsumowanie i roli aktywacji](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Kliknij przycisk aktywacji określoną rolę, aby wyświetlić szczegóły i odpowiadającego im działania zasobów platformy Azure, który wystąpił podczas, gdy ten użytkownik był aktywny.

    ![Uaktywnienie roli zaznaczone i Szczeg. działania wyświetlane według daty](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Wyeksportowanie przypisań roli z elementami podrzędnymi

Może być wymagań zgodności, gdzie należy podać pełną listę przypisań ról do obrachunkowego. PIM pozwala przesyłać zapytania przypisania ról w określonego zasobu, który zawiera przypisania roli dla wszystkich zasobów podrzędnych. Wcześniej było trudne dla administratorów uzyskać pełną listę przypisań ról w ramach subskrypcji i powodowały one na wyeksportowanie przypisań roli dla każdego określonego zasobu. Korzystając z usługi PIM, można wysyłać zapytania dotyczące wszystkich przypisań roli aktywnych i kwalifikujących się w ramach subskrypcji, w tym przypisania roli dla wszystkich grup zasobów i zasobów.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Kliknij zasób, aby eksportować przypisania roli, np. subskrypcji.

1. Kliknij przycisk **członków**.

1. Kliknij przycisk **wyeksportować** aby otworzyć okienko członkostwa eksportu.

    ![Okienko członkostwa eksportu, aby wyeksportować wszystkie elementy członkowskie](media/azure-pim-resource-rbac/export-membership.png)

1. Kliknij przycisk **wyeksportować wszystkie elementy członkowskie** Aby wyeksportować wszystkie przypisania roli w pliku CSV.

    ![Wyeksportowane przypisaniami ról w plików CSV, jak wyświetlić w programie Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Wyświetl historię inspekcji zasobów

Inspekcja zasobu zapewnia wgląd w całą aktywność roli dla zasobu.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Kliknij zasób, którą chcesz wyświetlić historię inspekcji.

1. Kliknij przycisk **inspekcja zasobu**.

1. Filtruj historię za pomocą wstępnie zdefiniowanych datę lub zakres niestandardowy.

    ![Lista inspekcji zasobów za pomocą filtrów](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Aby uzyskać **typ inspekcji**, wybierz opcję **Aktywuj (przypisane + aktywowany)** .

    ![Listy inspekcji zasobów są filtrowane według typu inspekcji Aktywuj](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. W obszarze **akcji**, kliknij przycisk **(działanie)** dla użytkownika, aby wyświetlić szczegóły dotyczące działania tego użytkownika w ramach zasobów platformy Azure.

    ![Szczegóły działania użytkownika dla określonej akcji](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Wyświetl moje inspekcji

Moja Inspekcja umożliwia wyświetlenie swoje działania w roli osobistej.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Kliknij zasób, którą chcesz wyświetlić historię inspekcji.

1. Kliknij przycisk **Moja inspekcja**.

1. Filtruj historię za pomocą wstępnie zdefiniowanych datę lub zakres niestandardowy.

    ![Lista inspekcji dla bieżącego użytkownika](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Kolejne kroki

- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
- [Zatwierdź lub Odrzuć żądania dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-approval-workflow.md)
- [Wyświetl historię inspekcji dla ról usługi Azure AD w usłudze PIM](pim-how-to-use-audit-log.md)
