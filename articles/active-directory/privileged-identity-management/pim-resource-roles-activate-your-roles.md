---
title: Uaktywnij Moje role zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501664"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Uaktywnij Moje role zasobów platformy Azure w usłudze PIM

Członkowie kwalifikowania się do roli dla zasobów platformy Azure, za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), można zaplanować aktywacji dla przyszłej daty i godziny. Można również wybrać czas trwania aktywacji określonych w maksymalnym (skonfigurowane przez administratorów).

W tym artykule jest przeznaczona dla uczestników, którzy muszą aktywować swoją rolę zasobu platformy Azure w usłudze PIM.

## <a name="activate-a-role"></a>Uaktywnij rolę

Gdy zachodzi potrzeba podjęcia roli zasobów platformy Azure, możesz poprosić aktywacji przy użyciu **Moje role** opcji nawigacji w usłudze PIM.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **usługi Azure AD Privileged Identity Management**. Aby uzyskać informacje dotyczące sposobu dodawania kafelka PIM do pulpitu nawigacyjnego, zobacz [rozpocząć korzystanie z usługi PIM](pim-getting-started.md).

1. Kliknij przycisk **Moje role**.

    ![Strona Moje role przedstawiający role można uaktywnić](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Kliknij przycisk **role zasobów platformy Azure** umożliwia wyświetlenie listy z ról uprawnionych zasobów platformy Azure.

   ![Moje role — strona role zasobów platformy Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. W **role zasobów platformy Azure** listy, Znajdź rolę, którą chcesz aktywować.

    ![Role zasobów platformy Azure — Moja lista kwalifikujące się role](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Kliknij przycisk **Aktywuj** aby otworzyć okienko aktywacji.

1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego (MFA), kliknij **zweryfikować swoją tożsamość przed kontynuowaniem**. Musisz uwierzytelnić się jeden raz na sesję.

    ![Zweryfikuj moją tożsamość za pomocą usługi MFA przed aktywację roli](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Kliknij przycisk **Zweryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami w celu zapewnienia dodatkowej weryfikacji zabezpieczeń.

    ![Ekran w celu zapewnienia weryfikacji zabezpieczeń, takie jak kod PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Jeśli chcesz określić ograniczonym zakresie, kliknij przycisk **zakres** aby otworzyć okienko filtru zasobów.

    Jest najlepszym rozwiązaniem, aby tylko żądania dostępu do zasobów, których potrzebujesz. Na okienka filtru zasobu można określić grupy zasobów lub zasobów, które muszą mieć dostęp do.

    ![Aktywuj — okienko filtru zasobu do określania zakresu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Jeśli to konieczne, należy określić czas rozpoczęcia aktywacji niestandardowych. Element członkowski zostanie aktywowany po wybranej godzinie.

1. W **Przyczyna** Wprowadź przyczynę żądania aktywacji.

    ![Ukończone okienko Aktywuj z zakresu, czas rozpoczęcia, czas trwania i przyczyny](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Kliknij pozycję **Aktywuj**.

    Jeśli rola wymaga zatwierdzenia, jest aktywowana i dodany do listy aktywnych ról. Jeśli chcesz korzystać z roli, wykonaj kroki opisane w następnej sekcji.

    Jeśli [rola wymaga zatwierdzenia](pim-resource-roles-approval-workflow.md) można aktywować, powiadomienie pojawi się w prawym górnym rogu przeglądarki informujące o tym, żądanie oczekuje na zatwierdzenie.

    ![Żądanie aktywacji jest powiadomienie o oczekujących na zatwierdzenie](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Użyj roli od razu po aktywacji

W przypadku wszelkich opóźnień po wykonaniu aktywacji wykonaj następujące czynności, po aktywowaniu natychmiastowe korzystanie role zasobów platformy Azure.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **Moje role** umożliwia wyświetlenie listy z kwalifikujących się ról usługi Azure AD i role zasobów platformy Azure.

1. Kliknij przycisk **role zasobów platformy Azure**.

1. Kliknij przycisk **aktywnych ról** kartę.

1. Po uaktywnieniu roli Wyloguj się z portalu i zaloguj się ponownie.

    Roli powinno być teraz dostępne do użycia.

## <a name="view-the-status-of-your-requests"></a>Wyświetl stan swoich żądań

Można wyświetlić stan Oczekujące żądania aktywowania.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **Moje żądania** umożliwia wyświetlenie listy roli usługi Azure AD i rolach usługi Azure resource żądań.

    ![Moje żądania - przedstawiający oczekujące żądania stronę zasobów platformy Azure](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Przewiń w prawo, aby wyświetlić **stan żądania** kolumny.

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie jest wymagane uaktywnienie roli, która wymaga zatwierdzenia, możesz anulować w dowolnym momencie oczekującego żądania.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **Moje żądania**.

1. Dla tej roli, którą chcesz anulować, kliknij przycisk **anulować** łącza.

    Gdy klikniesz przycisk Anuluj, żądanie zostanie anulowane. Aby aktywować rolę ponownie, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Moja lista żądania przy użyciu akcji anulowania wyróżniony](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnienia po aktywacji roli

Po aktywacji roli w usłudze PIM aktywacji nie może natychmiast propagowane do wszystkich portalach, które wymagają ról uprzywilejowanych. Czasami nawet wtedy, gdy zmiany są rozprowadzane, sieci web, pamięci podręcznej w portalu może spowodować zmiany nie wpływają od razu. Jeśli proces aktywacji jest opóźnione, Oto co należy zrobić.

1. Wyloguj się z witryny Azure portal, a następnie zaloguj się ponownie.

    Podczas aktywacji roli zasobów platformy Azure, zobaczysz etapy proces aktywacji. Po zakończeniu wszystkich etapów zobaczysz **Wyloguj** łącza. Aby się wylogować, można użyć tego łącza. Rozwiąże to najczęściej activation opóźnienia.

1. W usłudze PIM Sprawdź, czy jesteś członkiem roli.

## <a name="next-steps"></a>Kolejne kroki

- [Rozszerzanie lub odnawianie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-renew-extend.md)
- [Uaktywnij Moje role usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md)
