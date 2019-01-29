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
ms.subservice: pim
ms.date: 11/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e37435b0f2d52e2015caf3d2e0db6db0dba60c75
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187355"
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

1. Kliknij pozycję **Aktywuj**.

    Jeśli rola wymaga zatwierdzenia, jest aktywowana i dodany do listy aktywnych ról. Jeśli chcesz użyć roli od razu, postępuj zgodnie z instrukcjami w następnej sekcji.

    Jeśli [rola wymaga zatwierdzenia](pim-resource-roles-approval-workflow.md) można aktywować, powiadomienie pojawi się w prawym górnym rogu przeglądarki informujące o tym, żądanie oczekuje na zatwierdzenie.

    ![Żądania oczekujące powiadomienia](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Użyj roli od razu po aktywacji

Po aktywacji roli w usłudze PIM trwa co najmniej 10 minut, zanim będzie można uzyskać dostęp do żądanego portalu administracyjnego lub wykonywania funkcji w ramach określonego obciążenia administracyjnego. Aby wymusić aktualizację swoje uprawnienia, użyj **dostęp do aplikacji** stronie zgodnie z opisem w poniższych krokach.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **dostęp do aplikacji** strony.

    ![Dostęp do aplikacji PIM — zrzut ekranu](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Kliknij przycisk **zasobów platformy Azure** link, aby ponownie otworzyć portalu na **wszystkie zasoby** strony.

    Po kliknięciu tego łącza, unieważnić bieżącej token i wymusić portalu Azure w celu uzyskania nowego tokenu, który powinien zawierać zaktualizowano uprawnienia.

## <a name="view-the-status-of-your-requests"></a>Wyświetl stan swoich żądań

Można wyświetlić stan Oczekujące żądania aktywowania.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **Moje żądania** umożliwia wyświetlenie listy ról katalogu usługi Azure AD i rolach usługi Azure resource żądań.

    ![Role katalogu usługi Azure AD i role zasobów platformy Azure — Moje żądania](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Przewiń w prawo, aby wyświetlić **stan żądania** kolumny.

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie jest wymagane uaktywnienie roli, która wymaga zatwierdzenia, możesz anulować w dowolnym momencie oczekującego żądania.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **Moje żądania**.

1. Dla tej roli, którą chcesz anulować, kliknij przycisk **anulować** łącza.

    Gdy klikniesz przycisk Anuluj, żądanie zostanie anulowane. Aby aktywować rolę ponownie, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Anulowanie oczekującego żądania](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="permissions-not-granted-after-activating-a-role"></a>Uprawnienia nie udzielone po aktywacji roli

Po aktywacji roli w usłudze PIM trwa co najmniej 10 minut, zanim będzie można uzyskać dostęp do żądanego portalu administracyjnego lub wykonywania funkcji w ramach określonego obciążenia administracyjnego. Aby wymusić aktualizację swoje uprawnienia, użyj **dostęp do aplikacji** stronie zgodnie z wcześniejszym opisem w sekcji [użyć roli natychmiast po wykonaniu aktywacji](#use-a-role-immediately-after-activation).

Aby uzyskać dodatkowe kroki rozwiązywania problemów, zobacz [Rozwiązywanie problemów z podwyższonym poziomem uprawnień](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>Nie można aktywować roli z powodu blokady zasobu

Jeśli komunikat o błędzie, zasób platformy Azure jest zablokowany podczas próby aktywowania roli, może to być spowodowane zasób w zakresie przypisania roli ma blokady zasobu. Blokady chronić zasoby przed przypadkowym usunięciem lub nieoczekiwanych zmian. Blokada zapobiega także PIM usunięcie przypisania roli dla zasobu z końcem okresu aktywacji. Ponieważ usługi PIM nie może działać prawidłowo po zastosowaniu blokadę, PIM użytkownicy nie uaktywnianie ról w zasobie. Istnieją dwa sposoby rozwiązywania tego problemu:

- Usuń blokadę, zgodnie z opisem w [blokowanie zasobów w celu uniemożliwienia nieoczekiwanych zmian](../../azure-resource-manager/resource-group-lock-resources.md).
- Jeśli chcesz zachować blokady były trwałe przypisania roli, lub użyj konta break szkła.

## <a name="next-steps"></a>Kolejne kroki

- [Rozszerzanie lub odnawianie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md)
- [Uaktywnij Moje role katalogu usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md)
