---
title: Uaktywnij Moje role zasobów platformy Azure w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak aktywować swoje role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666251"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Uaktywnij Moje role zasobów platformy Azure w usłudze PIM

Członkowie kwalifikowania się do roli dla zasobów platformy Azure, za pomocą usługi Azure AD Privileged Identity Management (PIM), można zaplanować aktywacji dla przyszłej daty i godziny. Można również wybrać czas trwania aktywacji określonych w maksymalnym (skonfigurowane przez administratorów).

W tym artykule jest przeznaczona dla uczestników, którzy muszą aktywować swoją rolę zasobu platformy Azure w usłudze PIM.

## <a name="activate-a-role"></a>Uaktywnij rolę

Gdy zachodzi potrzeba podjęcia roli zasobów platformy Azure, możesz poprosić aktywacji przy użyciu **Moje role** opcji nawigacji w usłudze PIM.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **usługi Azure AD Privileged Identity Management**. Aby uzyskać informacje dotyczące sposobu dodawania kafelka PIM do pulpitu nawigacyjnego, zobacz [rozpocząć korzystanie z usługi PIM](pim-getting-started.md).

1. Kliknij przycisk **Moje role** umożliwia wyświetlenie listy z kwalifikujących się ról katalogu usługi Azure AD i role zasobów platformy Azure.

    ![Role katalogu usługi Azure AD i role zasobów platformy Azure — Moje role](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. W **role zasobów platformy Azure** listy, Znajdź rolę, którą chcesz aktywować.

    ![Role zasobów platformy Azure — Moja lista ról](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Kliknij przycisk **Aktywuj** aby otworzyć okienko aktywacji.

1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego (MFA), kliknij **zweryfikować swoją tożsamość przed kontynuowaniem**. Musisz uwierzytelnić się jeden raz na sesję.

    ![Przed aktywację roli sprawdzić za pomocą usługi MFA](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Kliknij przycisk **Zweryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami w celu zapewnienia dodatkowej weryfikacji zabezpieczeń.

    ![Dodatkowa weryfikacja zabezpieczeń](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Jeśli chcesz określić ograniczonym zakresie, kliknij przycisk **zakres** aby otworzyć okienko filtru zasobów.

    Jest najlepszym rozwiązaniem, aby tylko żądania dostępu do zasobów, których potrzebujesz. Na okienka filtru zasobu można określić grupy zasobów lub zasobów, które muszą mieć dostęp do.

    ![Aktywuj — filtr zasobu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Jeśli to konieczne, należy określić czas rozpoczęcia aktywacji niestandardowych. Element członkowski zostanie aktywowany po wybranej godzinie.

1. W **Przyczyna** Wprowadź przyczynę żądania aktywacji.

    ![W okienku Aktywuj ukończone](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Kliknij przycisk **aktywować**.

    Jeśli rola wymaga zatwierdzenia, jest już aktywna, a rola jest wyświetlana na liście aktywnych ról. Jeśli [rola wymaga zatwierdzenia](pim-resource-roles-approval-workflow.md) można aktywować, powiadomienie pojawi się w prawym górnym rogu przeglądarki informujące o tym, żądanie oczekuje na zatwierdzenie.

    ![Żądania oczekujące powiadomienia](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Wyświetl stan swoich żądań

Można wyświetlić stan Oczekujące żądania aktywowania.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **Moje żądania** umożliwia wyświetlenie listy ról katalogu usługi Azure AD i rolach usługi Azure resource żądań.

    ![Role katalogu usługi Azure AD i role zasobów platformy Azure — Moje żądania](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Przewiń w prawo, aby wyświetlić **stan żądania** kolumny.

## <a name="use-a-role-immediately-after-activation"></a>Użyj roli od razu po aktywacji

Z powodu buforowania, aktywacji nie występują bezpośrednio w witrynie Azure portal, bez jego odświeżania. Jeśli musisz ograniczyć możliwość opóźnienia po aktywowaniu rolę, możesz użyć **dostęp do aplikacji** strony w portalu. Aplikacje dostępne na tej stronie sprawdź, czy nowego przypisania roli natychmiast.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **dostęp do aplikacji** strony.

    ![Dostęp do aplikacji PIM — zrzut ekranu](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Kliknij przycisk **zasobów platformy Azure** ponownie otworzyć portalu na **wszystkie zasoby** strony.

    Po kliknięciu tego łącza, możesz wymusić odświeżenie i sprawdzaj nowych przypisań ról zasobów platformy Azure.

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie jest wymagane uaktywnienie roli, która wymaga zatwierdzenia, możesz anulować w dowolnym momencie oczekującego żądania.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **Moje żądania**.

1. Dla tej roli, którą chcesz anulować, kliknij przycisk **anulować** łącza.

    Gdy klikniesz przycisk Anuluj, żądanie zostanie anulowane. Aby aktywować rolę ponownie, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Anulowanie oczekującego żądania](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>Kolejne kroki

- [Rozszerzanie lub odnawianie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md)
- [Uaktywnij Moje role katalogu usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md)