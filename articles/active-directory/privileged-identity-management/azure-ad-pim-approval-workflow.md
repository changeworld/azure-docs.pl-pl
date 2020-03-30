---
title: Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w usłudze PIM — usługa Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak zatwierdzać lub odrzucać żądania ról usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049014"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można skonfigurować role wymagające zatwierdzenia aktywacji i wybrać jednego lub wielu użytkowników lub grupy jako delegowane osoby zatwierdzające. Delegowane osoby zatwierdzające mają 24 godziny na zatwierdzenie żądań. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, uprawniony użytkownik musi ponownie przesłać nowe żądanie. 24-godzinnego przedziału czasu zatwierdzania nie można skonfigurować.

## <a name="determine-your-version-of-pim"></a>Określanie wersji programu PIM

Począwszy od listopada 2019 r. część ról usługi Azure AD usługi Zarządzanie tożsamościami uprzywilejowanymi jest aktualizowana do nowej wersji, która pasuje do środowiska dla ról platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmiany w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, które procedury, które należy wykonać w tym artykule, zależą od wersji zarządzania tożsamościami uprzywilejowanymi, które obecnie masz. Wykonaj kroki opisane w tej sekcji, aby ustalić, którą wersję zarządzania tożsamościami uprzywilejowanymi masz. Po poznaniu wersji zarządzania tożsamościami uprzywilejowanymi można wybrać procedury w tym artykule, które są zgodne z tą wersją.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest w roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Jeśli u góry strony przeglądu masz baner, postępuj zgodnie z instrukcjami na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami na karcie **Poprzednia wersja.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dla ról usługi Azure AD.

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

Jako delegowana osoba zatwierdzająca otrzymasz powiadomienie e-mail, gdy żądanie roli usługi Azure AD oczekuje na zatwierdzenie. Można wyświetlić te oczekujące żądania w zarządzanie tożsamością uprzywilejowaną.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **pozycję Zatwierdź żądania**.

    ![Zatwierdzanie żądań — strona z żądaniem przejrzenia ról usługi Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    W sekcji **Żądania aktywacji ról** zostanie wyświetlona lista żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Znajdź i wybierz żądanie, które chcesz zatwierdzić. Zostanie wyświetlona strona zatwierdzania lub odrzucania.

    ![Zatwierdzanie żądań — okienko zatwierdzania lub odmów z detalami i polem Uzasadnienie](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. W polu **Uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz **pozycję Zatwierdź**. Otrzymasz powiadomienie o zatwierdzeniu na platformie Azure.

    ![Zatwierdzone zostało powiadomienie o zatwierdzeniu z powiadomieniem o zatwierdzeniu](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
- Administratorzy globalni i administratorzy ról uprzywilejowanych są powiadamiani, gdy zatwierdzony użytkownik staje się aktywny w swojej roli.

>[!NOTE]
>Administrator roli globalnej lub administrator roli uprzywilejowanej, który uważa, że zatwierdzony użytkownik nie powinien być aktywny, może usunąć przypisanie aktywnego roli w zarządzania tożsamościami uprzywilejowanymi. Mimo że administratorzy nie są powiadamiani o oczekujących żądaniach, chyba że są osobami zatwierdzających, mogą wyświetlać i anulować wszystkie oczekujące żądania dla wszystkich użytkowników, wyświetlając oczekujące żądania w zarządzania tożsamościami uprzywilejowanymi.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

Jako delegowana osoba zatwierdzająca otrzymasz powiadomienie e-mail, gdy żądanie roli usługi Azure AD oczekuje na zatwierdzenie. Można wyświetlić te oczekujące żądania w zarządzanie tożsamością uprzywilejowaną.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Kliknij pozycję **Role usługi Azure AD**.

1. Kliknij **przycisk Zatwierdź żądania**.

    ![Role usługi Azure AD — zatwierdzanie żądań](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Zobaczysz listę żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Zaznacz żądania, które chcesz zatwierdzić, a następnie kliknij przycisk **Zatwierdź,** aby otworzyć okienko Zatwierdź wybrane żądania.

    ![Lista Zatwierdzanie żądań z wyróżnioną opcją Zatwierdź](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. W polu **Zatwierdź przyczynę** wpisz przyczynę.

    ![Zatwierdzanie zaznaczonych żądań z powodu zatwierdzania](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kliknij **przycisk Zatwierdź**.

    Symbol statusu zostanie zaktualizowany za zgodą użytkownika.

    ![Okienko Zatwierdzanie wybranych żądań po kliknięciu przycisku Zatwierdź](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Odmów żądań

1. Zaznacz żądania, które chcesz odrzucić, a następnie kliknij przycisk **Odmów,** aby otworzyć okienko Odmów wybrane żądania.

    ![Lista zatwierdzania żądań z wyróżnioną opcją Odmów](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. W polu **Przyczyna odmowy** wpisz przyczynę.

    ![Odmów zaznaczonego okienka żądań z przyczyną odmowy](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Wybierz **pozycję Odmów**.

    Symbol statusu zostanie zaktualizowany o odmowę.

---

## <a name="next-steps"></a>Następne kroki

- [Powiadomienia e-mail w zarządzania tożsamościami uprzywilejowanymi](pim-email-notifications.md)
- [Zatwierdzanie lub odrzucanie żądań ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-approval-workflow.md)
