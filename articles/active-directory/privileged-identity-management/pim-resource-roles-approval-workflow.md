---
title: Zatwierdzanie żądań ról zasobów platformy Azure w usłudze PIM — Usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak zatwierdzać lub odrzucać żądania ról zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e779f633efccf7b594c193e165a584d22b1d653b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021966"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Zatwierdzanie lub odrzucanie żądań ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Za pomocą usługi Uprzywilejowane zarządzanie tożsamościami (PIM) w usłudze Azure Active Directory (Azure AD) można skonfigurować role wymagające zatwierdzenia aktywacji i wybrać użytkowników lub grupy z organizacji usługi Azure AD jako delegowanych osób zatwierdzających. Zaleca się wybranie co najmniej dwóch osób zatwierdzających dla każdej roli w celu zmniejszenia obciążenia administratora ról uprzywilejowanych. Delegowane osoby zatwierdzające mają 24 godziny na zatwierdzenie żądań. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, uprawniony użytkownik musi ponownie przesłać nowe żądanie. 24-godzinnego przedziału czasu zatwierdzania nie można skonfigurować.

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dla ról zasobów platformy Azure.

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

Jako delegowana osoba zatwierdzająca otrzymasz powiadomienie e-mail, gdy żądanie roli zasobu platformy Azure oczekuje na zatwierdzenie. Można wyświetlić te oczekujące żądania w zarządzanie tożsamością uprzywilejowaną.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **pozycję Zatwierdź żądania**.

    ![Zatwierdzanie żądań — strona zasobów platformy Azure z żądaniem przejrzenia](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    W sekcji **Żądania aktywacji ról** zostanie wyświetlona lista żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Znajdź i wybierz żądanie, które chcesz zatwierdzić. Zostanie wyświetlona strona zatwierdzania lub odrzucania.

    ![Zatwierdzanie żądań — okienko zatwierdzania lub odmów z detalami i polem Uzasadnienie](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. W polu **Uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz **pozycję Zatwierdź**. Otrzymasz powiadomienie o zatwierdzeniu na platformie Azure.

    ![Zatwierdzone zostało powiadomienie o zatwierdzeniu z powiadomieniem o zatwierdzeniu](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Odmów żądań

1. Znajdź i wybierz żądanie, które chcesz odrzucić. Zostanie wyświetlona strona zatwierdzania lub odrzucania.

    ![Zatwierdzanie żądań — okienko zatwierdzania lub odmów z detalami i polem Uzasadnienie](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. W polu **Uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz **pozycję Odmów**. Pojawi się powiadomienie z odmową.

## <a name="workflow-notifications"></a>Powiadomienia o przepływie pracy

Oto kilka informacji o powiadomieniach o przepływie pracy:

- Osoby zatwierdzające są powiadamiane pocztą e-mail, gdy żądanie roli oczekuje na ich sprawdzenie. Powiadomienia e-mail zawierają bezpośredni link do żądania, w którym osoba zatwierdzająca może zatwierdzić lub odrzucić.
- Żądania są rozpoznawane przez pierwszą osobę zatwierdzającą, która zatwierdza lub odrzuca.
- Gdy osoba zatwierdzająca odpowiada na żądanie, wszystkie osoby zatwierdzające są powiadamiane o akcji.
- Administratorzy zasobów są powiadamiani, gdy zatwierdzony użytkownik staje się aktywny w swojej roli.

>[!Note]
>Administrator zasobów, który uważa, że zatwierdzony użytkownik nie powinien być aktywny, może usunąć przypisanie aktywnego roli w zarządzaniu tożsamościami uprzywilejowanymi. Chociaż administratorzy zasobów nie są powiadamiani o oczekujących żądaniach, chyba że są osobami zatwierdzających, mogą wyświetlać i anulować oczekujące żądania dla wszystkich użytkowników, wyświetlając oczekujące żądania w zarządzania tożsamościami uprzywilejowanymi.

## <a name="next-steps"></a>Następne kroki

- [Rozszerzanie lub odnawianie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-renew-extend.md)
- [Powiadomienia e-mail w zarządzania tożsamościami uprzywilejowanymi](pim-email-notifications.md)
- [Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](azure-ad-pim-approval-workflow.md)
