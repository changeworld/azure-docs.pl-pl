---
title: Zapraszanie gości i przypisz role zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapraszać użytkowników-gości zewnętrznych i przypisywać role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0afec1d6eded25a2d9b2389c950e2e21e06e0d54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307063"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Zapraszanie gości i przypisz role zasobów platformy Azure w usłudze PIM

Usługa Azure Active Directory (Azure AD) business-to-business (B2B) to zestaw funkcji w usłudze Azure AD, która umożliwia organizacjom współpracę z zewnętrznego gościa użytkowników (gości) i dostawców przy użyciu dowolnego konta. Łącząc B2B przy użyciu usługi Azure AD Privileged Identity Management (PIM), można zastosować wymagań dotyczących zgodności i nadzoru dla gości. Na przykład można użyć tych funkcji usługi PIM dla zadań usługi Azure identity z gości:

- Przypisywanie dostępu do określonych zasobów platformy Azure
- Włącz dostęp just in time
- Określ czas trwania i na końcu Data przypisania
- Wymagać uwierzytelniania Wieloskładnikowego na aktywnym przypisaniu lub aktywacji
- Wykonaj przeglądów dostępu
- Korzystanie z alertów i dzienników inspekcji

W tym artykule opisano, jak zapraszać gości do organizacji i zarządzanie nimi ich dostępu do zasobów platformy Azure przy użyciu Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Gdy możesz zapraszać gości?

Poniżej przedstawiono kilka przykładowych scenariuszy, gdy mogą zapraszać gości do Twojej organizacji:

- Zezwalaj na zewnętrznego dostawcy na własny rachunek, który zawiera tylko konto e-mail, dostęp do zasobów platformy Azure dla projektu.
- Zezwalaj na partnera usługi zewnętrzne w dużej organizacji korzystającej z usług federacyjnych Active Directory w środowisku lokalnym uzyskiwanie dostępu do wydatków aplikacji.
- Zezwalaj na pracowników działu pomocy technicznej nie znajduje się w Twojej organizacji (np. pomocy technicznej firmy Microsoft) do tymczasowego dostępu do swoich zasobów platformy Azure do rozwiązywania problemów.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak działa współpraca za pomocą funkcji B2B goście pracy?

Gdy używasz współpracy B2B, możesz Zaproś użytkownika zewnętrznego dla Twojej organizacji jako Gość. Gość pojawia się w Twojej organizacji, ale gościa nie ma żadnych poświadczeń skojarzonych z nim. Zawsze, gdy Gość musi zostać uwierzytelniony, muszą uwierzytelnić się w swojej organizacji macierzystej a nie w Twojej organizacji. Oznacza to, że jeśli gościa nie ma już dostęp do swoich organizacji macierzystej, również stracą dostęp do Twojej organizacji. Na przykład jeśli gościa swojej organizacji, automatycznie stracą dostęp do żadnych zasobów udostępnionych je w usłudze Azure AD bez konieczności podejmować żadnych działań. Aby uzyskać więcej informacji na temat B2B, zobacz [co to jest dostęp użytkowników-gości w usłudze Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B i gościa](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Sprawdź ustawienia współpracy gościa

Aby upewnić się, że mogą zapraszać gości w Twojej organizacji, należy sprawdzić ustawienia współpracy gościa.

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. Kliknij przycisk **usługi Azure Active Directory** > **ustawienia użytkownika**.

1. Kliknij przycisk **Zarządzaj ustawieniami współpracy zewnętrznej**.

    ![Ustawienia zewnętrznej współpracy](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Upewnij się, że **Administratorzy i użytkownicy o roli zapraszającego gości mogą zapraszać** przełącznik ma wartość **tak**.

## <a name="invite-a-guest-and-assign-a-role"></a>Zapraszanie gościa i przypisanie roli

Korzystając z usługi PIM, możesz zapraszanie gościa i ustawiać ich jako uprawnionych dla ról zasobów platformy Azure, podobnie jak użytkownika elementu członkowskiego.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) lub [Administrator użytkowników](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) roli.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Użyj **filtr zasobu** Aby filtrować listę zarządzanych zasobów.

1. Kliknij zasób, który chcesz zarządzać, takich jak zasobu, grupy zasobów, subskrypcji lub grupy zarządzania.

    Wystarczy jakie gościa należy ustawić zakresu.

1. W obszarze Zarządzanie, kliknij przycisk **role** Aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Role zasobów platformy Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kliknij rolę minimalne, użytkownik będzie musiał.

    ![Wybrana rola](./media/pim-resource-roles-external-users/selected-role.png)

1. Na stronie roli kliknij **Dodawanie elementu członkowskiego** otworzyć nowe okienko przypisania.

1. Kliknij przycisk **zaznacz element lub grupę**.

    ![Zaznacz element lub grupę](./media/pim-resource-roles-external-users/select-member-group.png)

1. Aby zapraszanie gościa, kliknij przycisk **zaprosić**.

    ![Zapraszanie gościa](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po wybraniu gość kliknij **zaprosić**.

    Gość powinny zostać dodane jako wybrany element członkowski.

1. W **zaznacz element lub grupę** okienku kliknij **wybierz**.

1. W **ustawienia członkostwa** okienku, wybierz typ przypisania i czas trwania.

    ![Ustawienia członkostwa](./media/pim-resource-roles-external-users/membership-settings.png)

1. Aby dokończyć przypisanie, kliknij przycisk **gotowe** i następnie **Dodaj**.

    Przypisanie roli gościa pojawi się na liście ról.

    ![Przypisanie roli dla gościa](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktywacja roli jako Gość

Jako użytkownika zewnętrznego musisz najpierw zaakceptować zaproszenie do organizacji usługi Azure AD i prawdopodobnie aktywowania roli.

1. Otwórz wiadomość e-mail z Twoje zaproszenie. Wiadomość e-mail będzie wyglądać podobnie do poniższego.

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

## <a name="view-activity-for-a-guest"></a>Wyświetl działania dla gościa

Podobnie jak użytkownika elementu członkowskiego możesz wyświetlić dzienniki inspekcji do śledzenia działania gości.

1. Jako administrator Otwórz PIM, a następnie wybierz zasób, który został udostępniony.

1. Kliknij przycisk **inspekcja zasobu** Aby wyświetlić działania dla tego zasobu. Poniżej przedstawiono przykład działania dotyczące grupy zasobów.

    ![Inspekcja zasobu](./media/pim-resource-roles-external-users/audit-resource.png)

1. Aby wyświetlić działania dla gości, kliknij **usługi Azure Active Directory** > **użytkowników** > Nazwa gościa.

1. Kliknij przycisk **dzienniki inspekcji** na przeglądanie dzienników inspekcji dla organizacji. Jeśli to konieczne, możesz określić filtry.

    ![Inspekcja organizacji](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Kolejne kroki

- [Przypisywanie ról administratora w usłudze Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Co to jest dostęp użytkowników-gości w usłudze Azure Active Directory B2B?](../b2b/what-is-b2b.md)
