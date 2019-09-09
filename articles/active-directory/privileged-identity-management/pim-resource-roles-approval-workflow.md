---
title: Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak zatwierdzać lub odrzucać żądania dotyczące ról zasobów platformy Azure w programie Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b39434f8763e44a126f74ac9a19596e4413ae9c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804273"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można skonfigurować role, aby wymagały zatwierdzenia dla aktywacji, i wybrać jednego lub wielu użytkowników lub grupy jako delegowane osoby zatwierdzające. Delegowane osoby zatwierdzające mają 24 godziny na zatwierdzanie żądań. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, wówczas uprawniony użytkownik musi ponownie przesłać nowe żądanie. Nie można skonfigurować okna czasu zatwierdzania 24-godzinnego.

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dotyczące ról zasobów platformy Azure.

## <a name="view-pending-requests"></a>Wyświetl oczekujące żądania

W przypadku delegowanej osoby zatwierdzającej otrzymasz powiadomienie e-mail, gdy żądanie roli zasobu platformy Azure oczekuje na zatwierdzenie. Te oczekujące żądania można wyświetlić w usłudze PIM.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **Zatwierdź żądania**.

    ![Zatwierdź żądania — na stronie zasobów platformy Azure zostanie wyświetlona prośba o przejrzenie](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    W sekcji **żądania aktywacji roli** zobaczysz listę żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdź żądania

1. Znajdź i kliknij żądanie, które chcesz zatwierdzić. Zostanie wyświetlone okienko zatwierdzanie lub odmawianie.

    ![Okienko zatwierdzanie żądań — zatwierdzanie lub odrzucanie z użyciem pola szczegóły i uzasadnienia](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. W polu **uzasadnienie** wpisz przyczynę.

1. Kliknij przycisk **Zatwierdź**.

    Zostanie wyświetlone powiadomienie z zatwierdzeniem.

    ![Zatwierdź powiadomienie z zatwierdzeniem żądania](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Odmów żądań

1. Znajdź i kliknij żądanie, które chcesz odmówić. Zostanie wyświetlone okienko zatwierdzanie lub odmawianie.

    ![Okienko zatwierdzanie żądań — zatwierdzanie lub odrzucanie z użyciem pola szczegóły i uzasadnienia](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. W polu **uzasadnienie** wpisz przyczynę.

1. Kliknij przycisk **Odmów**.

    Zostanie wyświetlone powiadomienie z odmową.

## <a name="workflow-notifications"></a>Powiadomienia przepływu pracy

Oto kilka informacji o powiadomieniach dotyczących przepływu pracy:

- Wszystkie elementy członkowskie listy osób zatwierdzających są powiadamiane pocztą e-mail, gdy żądanie dotyczące roli oczekuje na przegląd. Powiadomienia e-mail zawierają bezpośredni link do żądania, gdzie osoba zatwierdzająca może zatwierdzić lub odmówić.
- Żądania są rozwiązywane przez pierwszego członka listy, który zatwierdza lub odmówi.
- Gdy osoba zatwierdzająca odpowie na żądanie, wszyscy członkowie listy osoby zatwierdzającej są powiadamiani o akcji.
- Administratorzy zasobów są powiadamiani, gdy zatwierdzony członek zostanie uaktywniony w roli.

>[!Note]
>Administrator zasobów, który uważa, że zatwierdzony element członkowski nie powinien być aktywny, może usunąć aktywne przypisanie roli w programie PIM. Mimo że administratorzy zasobów nie są powiadamiani o oczekujących żądaniach, chyba że są członkami listy osób zatwierdzających, mogą wyświetlać i anulować oczekujące żądania wszystkich użytkowników, wyświetlając oczekujące żądania w usłudze PIM. 

## <a name="next-steps"></a>Następne kroki

- [Przedłużanie lub odnowienie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md)
- [Powiadomienia e-mail w usłudze PIM](pim-email-notifications.md)
- [Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w usłudze PIM](azure-ad-pim-approval-workflow.md)
