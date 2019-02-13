---
title: Zaproś użytkowników zewnętrznych i przypisz role zasobów platformy Azure w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapraszać użytkowników zewnętrznych i przypisywać role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90d0d3d3f484044a0ffbab7a3c24a76c40aa74c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208287"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>Zaproś użytkowników zewnętrznych i przypisz role zasobów platformy Azure w usłudze PIM

Usługa Azure Active Directory (Azure AD) business-to-business (B2B) to zestaw funkcji w usłudze Azure AD, która umożliwia organizacjom współpracę z użytkowników zewnętrznych i dostawców przy użyciu dowolnego konta. Możesz połączyć rozwiązanie B2B z usługi Azure AD Privileged Identity Management (PIM), można zastosować wymagań dotyczących zgodności i nadzoru dla użytkowników zewnętrznych. Na przykład można użyć tych funkcji usługi PIM dla zasobów platformy Azure, użytkownikom zewnętrznym:

- Przypisywanie dostępu do określonych zasobów platformy Azure
- Włącz dostęp just in time
- Określ czas trwania i na końcu Data przypisania
- Wymagać uwierzytelniania Wieloskładnikowego na aktywnym przypisaniu lub aktywacji
- Wykonaj przeglądów dostępu
- Korzystanie z alertów i dzienników inspekcji

W tym artykule opisano jak Zaproś użytkownika zewnętrznego do katalogu i zarządzać ich dostępu do zasobów platformy Azure za pomocą usługi PIM.

## <a name="when-would-you-invite-external-users"></a>Gdy możesz zaprosić użytkowników zewnętrznych?

Poniżej przedstawiono kilka przykładowych scenariuszy, gdy może zapraszać użytkowników zewnętrznych z katalogiem:

- Zezwalaj na zewnętrznego dostawcy na własny rachunek, który zawiera tylko konto e-mail, dostęp do zasobów platformy Azure dla projektu.
- Zezwalaj na partnera usługi zewnętrzne w dużej organizacji korzystającej z usług federacyjnych Active Directory w środowisku lokalnym uzyskiwanie dostępu do wydatków aplikacji.
- Zezwalaj na pracowników działu pomocy technicznej nie znajduje się w Twojej organizacji (np. pomocy technicznej firmy Microsoft) do tymczasowego dostępu do swoich zasobów platformy Azure do rozwiązywania problemów.

## <a name="how-does-external-collaboration-using-b2b-work"></a>Jak działa współpracy zewnętrznej za pomocą B2B?

Użycie B2B, możesz zaprosić użytkownika zewnętrznego do katalogu. Użytkownik zewnętrzny pojawia się w katalogu, ale użytkownik nie ma żadnych poświadczeń skojarzonych z nim. Zawsze, gdy użytkownik zewnętrzny musi zostać uwierzytelniony, muszą uwierzytelnić się w katalogu macierzystego, a nie katalogiem. Oznacza to, że jeśli użytkownik zewnętrzny nie ma już dostęp do własnego katalogu macierzystego, automatycznie stracą dostęp do katalogu. Na przykład jeśli zewnętrzny użytkownik opuszcza swojej organizacji, automatycznie stracą dostęp do żadnych zasobów udostępnionych im w katalogu bez konieczności podejmować żadnych działań. Aby uzyskać więcej informacji na temat B2B, zobacz [co to jest dostęp użytkowników-gości w usłudze Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B i użytkowników zewnętrznych](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>Sprawdź ustawienia zewnętrznej współpracy

Aby upewnić się, że użytkownicy zewnętrzni mogą zapraszać do katalogu, należy sprawdzić ustawienia współpracy zewnętrznej.

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. Kliknij przycisk **usługi Azure Active Directory** > **ustawienia użytkownika**.

1. Kliknij przycisk **Zarządzaj ustawieniami współpracy zewnętrznej**.

    ![Ustawienia zewnętrznej współpracy](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Upewnij się, że **Administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać** przełącznik ma wartość **tak**.

## <a name="invite-an-external-user-and-assign-a-role"></a>Zaproś użytkownika zewnętrznego i przypisywanie roli

Za pomocą usługi PIM, możesz zaprosić użytkownika zewnętrznego i ustawiać ich jako uprawnionych dla ról zasobów platformy Azure, podobnie jak użytkownika elementu członkowskiego.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) lub [Administrator kont użytkowników](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator) roli.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Użyj **filtr zasobu** Aby filtrować listę zarządzanych zasobów.

1. Kliknij zasób, który chcesz zarządzać, takich jak zasobu, grupy zasobów, subskrypcji lub grupy zarządzania.

    Wystarczy jakie użytkownika zewnętrznego, należy ustawić zakresu.

1. W obszarze Zarządzanie, kliknij przycisk **role** Aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kliknij rolę minimalne, użytkownik będzie musiał.

    ![Wybrana rola](./media/pim-resource-roles-external-users/selected-role.png)

1. Na stronie roli kliknij **Dodawanie elementu członkowskiego** otworzyć nowe okienko przypisania.

1. Kliknij przycisk **zaznacz element lub grupę**.

    ![Wybierz członka lub grupę](./media/pim-resource-roles-external-users/select-member-group.png)

1. Aby zaprosić użytkownika zewnętrznego, kliknij przycisk **zaprosić**.

    ![Zapraszanie gościa](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po określeniu użytkownika zewnętrznego, kliknij przycisk **zaprosić**.

    Użytkownik zewnętrzny należy dołączyć jako wybrany element członkowski.

1. W polu Wybierz panel lub grupę, kliknij przycisk **wybierz**.

1. W okienku ustawienia członkostwa wybierz typ przypisania i czasu trwania.

    ![Ustawienia członkostwa](./media/pim-resource-roles-external-users/membership-settings.png)

1. Aby dokończyć przypisanie, kliknij przycisk **gotowe** i następnie **Dodaj**.

    Przypisanie roli użytkownika zewnętrznego, pojawi się na liście ról.

    ![Przypisanie roli dla użytkownika zewnętrznego](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>Aktywacja roli jako użytkownik zewnętrzny

Jako użytkownik zewnętrzny musisz najpierw zaakceptować zaproszenia do katalogu usługi Azure AD i prawdopodobnie aktywować swoją rolę.

1. Otworzyć wiadomości e-mail za pomocą zaproszenia do katalogu. Wiadomość e-mail będzie wyglądać podobnie do poniższego.

    ![Wiadomość e-mail z zaproszeniem](./media/pim-resource-roles-external-users/email-invite.png)

1. Kliknij przycisk **wprowadzenie** łącze w wiadomości e-mail.

1. Po przejrzeniu uprawnienia kliknij **Akceptuj**.

    ![Przejrzyj uprawnienia](./media/pim-resource-roles-external-users/invite-accept.png)

1. Użytkownik może zostać poproszona o Zaakceptuj warunki użytkowania i określić, czy nie wylogowuj.

    Zostanie otwarta witryna Azure portal. Jeśli po prostu kwalifikują się do roli nie będzie mieć dostęp do zasobów.

1. Aby aktywować swoją rolę, Otwórz wiadomość e-mail z Twojej uaktywnienie roli łącza. Wiadomość e-mail będzie wyglądać podobnie do poniższego.

    ![Wiadomość e-mail z zaproszeniem](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Kliknij przycisk **Aktywuj rolę** otworzyć kwalifikujące się role w usłudze PIM.

    ![Moje role - kwalifikujących się](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. W obszarze akcji, kliknij przycisk **Aktywuj** łącza.

    W zależności od ustawień roli należy określić pewne informacje w celu uaktywnienia roli.

1. Po określeniu ustawień dla roli, kliknij przycisk **Aktywuj** aktywowania roli.

    ![Uaktywnij rolę](./media/pim-resource-roles-external-users/activate-role.png)

    Chyba że administrator musi zatwierdzić Twoje zgłoszenie, powinny mieć dostęp do określonych zasobów.

## <a name="view-activity-for-an-external-user"></a>Wyświetl aktywność użytkownika zewnętrznego

Podobnie jak użytkownika elementu członkowskiego możesz wyświetlić dzienniki inspekcji, aby śledzić działania użytkowników zewnętrznych.

1. Jako administrator Otwórz PIM, a następnie wybierz zasób, który został udostępniony.

1. Kliknij przycisk **inspekcja zasobu** Aby wyświetlić działania dla tego zasobu. Poniżej przedstawiono przykład działania dotyczące grupy zasobów.

    ![Inspekcja zasobu](./media/pim-resource-roles-external-users/audit-resource.png)

1. Aby wyświetlić działania dla użytkownika zewnętrznego, kliknij **usługi Azure Active Directory** > **użytkowników** > użytkownika zewnętrznego.

1. Kliknij przycisk **dzienniki inspekcji** na przeglądanie dzienników inspekcji dla katalogu. Jeśli to konieczne, możesz określić filtry.

    ![Inspekcja katalogu](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Kolejne kroki

- [Przypisywanie ról katalogu usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Co to jest dostęp użytkowników-gości w usłudze Azure Active Directory B2B?](../b2b/what-is-b2b.md)
