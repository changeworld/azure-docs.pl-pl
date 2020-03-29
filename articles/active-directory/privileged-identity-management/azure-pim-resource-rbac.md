---
title: Wyświetlanie raportu inspekcji ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi — usługa Azure AD | Dokumenty firmy Microsoft
description: Wyświetlanie historii aktywności i inspekcji ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329632"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Wyświetlanie historii działań i inspekcji ról zasobów platformy Azure w dziale Zarządzanie tożsamościami uprzywilejowanymi

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można wyświetlać działania, aktywacje i historię inspekcji ról zasobów platformy Azure w organizacji. Obejmuje to subskrypcje, grupy zasobów, a nawet maszyny wirtualne. Każdy zasób w witrynie Azure portal, który korzysta z funkcji kontroli dostępu opartej na rolach platformy Azure, może korzystać z funkcji zarządzania zabezpieczeniami i cyklem życia w bezpieczeństwie tożsamości uprzywilejowanych.

> [!NOTE]
> Jeśli twoja organizacja zleciła funkcje zarządzania na zewnątrz dostawcy usług, który korzysta z [usługi Azure zarządzanie zasobami delegowanymi,](../../lighthouse/concepts/azure-delegated-resource-management.md)przypisania ról autoryzowane przez tego dostawcę usług nie będą wyświetlane w tym miejscu.

## <a name="view-activity-and-activations"></a>Wyświetlanie aktywności i aktywacji

Aby zobaczyć, jakie akcje wykonał określony użytkownik w różnych zasobach, można wyświetlić działanie zasobów platformy Azure skojarzone z danym okresem aktywacji.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Wybierz zasób, dla którego chcesz wyświetlić aktywność i aktywacje.

1. Wybierz **role** lub **członków**.

1. Wybierz użytkownika.

    Zobaczysz podsumowanie działań użytkownika w zasobach platformy Azure według daty. Pokazuje również ostatnie aktywacje roli w tym samym okresie czasu.

    ![Szczegóły użytkownika z podsumowaniem aktywności zasobów i aktywacjami ról](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Wybierz aktywację określonej roli, aby wyświetlić szczegóły i odpowiadające mu działania związane z zasobami platformy Azure, które wystąpiły, gdy ten użytkownik był aktywny.

    [![Wybrana aktywacja roli i szczegóły działania](media/azure-pim-resource-rbac/export-membership.png "Wybrana aktywacja roli i szczegóły działania")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Eksportowanie przypisań ról z dziećmi

Może być wymagane wymagania dotyczące zgodności, gdzie należy podać pełną listę przypisań ról audytorom. Zarządzanie tożsamościami uprzywilejowanymi umożliwia wykonywanie zapytań o przypisania ról w określonym zasobie, który obejmuje przypisania ról dla wszystkich zasobów podrzędnych. Wcześniej administratorom trudno było uzyskać pełną listę przypisań ról dla subskrypcji i musieli eksportować przypisania ról dla każdego określonego zasobu. Za pomocą zarządzania tożsamościami uprzywilejowanymi można wyszukiwać wszystkie aktywne i kwalifikujące się przypisania ról w ramach subskrypcji, w tym przypisania ról dla wszystkich grup zasobów i zasobów.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Wybierz zasób, dla którego chcesz wyeksportować przypisania ról, na przykład subskrypcję.

1. Wybierz **członków**.

1. Wybierz **pozycję Eksportuj,** aby otworzyć okienko Eksportuj członkostwo.

    [![Eksportuj okienko członkostwa, aby wyeksportować wszystkich członków](media/azure-pim-resource-rbac/export-membership.png "Eksportowanie strony członkostwa w celu wyeksportowania wszystkich członków")](media/azure-pim-resource-rbac/export-membership.png)

1. Wybierz **pozycję Eksportuj wszystkich członków,** aby wyeksportować wszystkie przypisania ról w pliku CSV.

    ![Eksportowane przypisania ról w pliku CSV jako wyświetlane w programie Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Wyświetlanie historii inspekcji zasobów

Inspekcja zasobów zapewnia widok wszystkich działań roli dla zasobu.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Wybierz zasób, dla którego chcesz wyświetlić historię inspekcji.

1. Wybierz **inspekcję zasobów**.

1. Filtruj historię przy użyciu wstępnie zdefiniowanej daty lub zakresu niestandardowego.

    [![Lista inspekcji zasobów z filtrami](media/azure-pim-resource-rbac/rbac-resource-audit.png "Lista inspekcji zasobów z filtrami")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. W przypadku **typu Inspekcja**wybierz **pozycję Aktywuj (Przypisano + Aktywowano)**.

    [![Lista inspekcji zasobów filtrowana według aktywowania typu](media/azure-pim-resource-rbac/rbac-audit-activity.png "Lista inspekcji zasobów filtrowana według uaktywniania")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![inspekcji Lista inspekcji zasobów filtrowana według aktywowania typu inspekcji](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. W obszarze **Akcja**kliknij **(działanie)** dla użytkownika, aby wyświetlić szczegóły aktywności tego użytkownika w zasobach platformy Azure.

    ![Szczegóły aktywności użytkownika dla określonej akcji](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Wyświetlanie audytu

Mój audyt umożliwia wyświetlanie osobistej aktywności roli.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Wybierz zasób, dla którego chcesz wyświetlić historię inspekcji.

1. Wybierz **pozycję Mój audyt**.

1. Filtruj historię przy użyciu wstępnie zdefiniowanej daty lub zakresu niestandardowego.

    [![Lista inspekcji dla bieżącego użytkownika](media/azure-pim-resource-rbac/my-audit-time.png "Lista inspekcji dla bieżącego użytkownika")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Dostęp do historii inspekcji wymaga roli administratora globalnego lub administratora ról uprzywilejowanych.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Uzyskaj przyczynę, osobę zatwierdzaącą i numer biletu dla zdarzeń zatwierdzenia

1. Zaloguj się do [witryny Azure Portal](https://aad.portal.azure.com) przy za pomocą uprawnień administratora roli roli uprzywilejowanej i otwórz usługę Azure AD.
1. Wybierz pozycję **Dzienniki inspekcji**.
1. Użyj filtru **Usługa,** aby wyświetlić tylko zdarzenia inspekcji usługi zarządzania tożsamościami uprzywilejowanymi. Na stronie **Dzienniki inspekcji** można:

    - Zobacz przyczynę zdarzenia inspekcji w kolumnie **Przyczyna stanu.**
    - Zobacz osobę zatwierdzającą w **zainicjowane przez (aktora)** kolumny dla zdarzenia "dodaj członka do żądania roli zatwierdzone".

    [![Filtrowanie dziennika inspekcji dla usługi PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrowanie dziennika inspekcji dla usługi PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Wybierz zdarzenie dziennika inspekcji, aby wyświetlić numer biletu na karcie **Aktywność** w okienku **Szczegóły.**
  
    [![Sprawdź numer biletu dla zdarzenia inspekcji](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Sprawdź numer biletu dla zdarzenia inspekcji")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Można wyświetlić żądającego (osoba aktywującą rolę) na karcie **Obiekty docelowe** w okienku **Szczegóły** dla zdarzenia inspekcji. Istnieją trzy typy docelowe ról zasobów platformy Azure:

    - Rola (**Typ** = Rola)
    - Osoba żądający (**Typ** = Inne)
    - Osoba zatwierdzająca (**typ** = użytkownik)

    [![Sprawdź typ obiektu docelowego](media/azure-pim-resource-rbac/audit-event-target-type.png "Sprawdź typ obiektu docelowego")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Zazwyczaj zdarzenie dziennika bezpośrednio nad zdarzeniem zatwierdzenia jest zdarzeniem "Dodaj element członkowski do roli ukończone", gdzie **zainicjowane przez (aktora)** jest z żądaniem. W większości przypadków nie trzeba znaleźć żądającego w żądaniu zatwierdzenia z punktu widzenia inspekcji.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-assign-roles.md)
- [Zatwierdzanie lub odrzucanie żądań ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-approval-workflow.md)
- [Wyświetlanie historii inspekcji ról usługi Azure AD w dziale Zarządzanie tożsamościami uprzywilejowanymi](pim-how-to-use-audit-log.md)
