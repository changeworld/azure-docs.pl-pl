---
title: Zapraszanie gości i przypisywanie ról zasobów platformy Azure w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak zapraszać zewnętrznych użytkowników-Gości i przypisywać role zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: f3e01b58a2a2fc6f93ae5ccc15e200a0cea69a0c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804214"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Zapraszanie użytkowników-Gości i przypisywanie ról zasobów platformy Azure w usłudze PIM

Azure Active Directory (Azure AD) Business-to-Business (B2B) to zestaw funkcji w usłudze Azure AD, które umożliwiają organizacjom współpracę z zewnętrznymi użytkownikami (gość) i dostawcami przy użyciu dowolnego konta. W przypadku łączenia B2B z Azure AD Privileged Identity Management (PIM) można nadal stosować wymagania dotyczące zgodności i zarządzania dla Gości. Można na przykład używać tych funkcji usług PIM na potrzeby zadań związanych z tożsamością platformy Azure z Gośćmi:

- Przypisywanie dostępu do określonych zasobów platformy Azure
- Włącz dostęp just in Time
- Określ czas trwania przypisania i datę zakończenia
- Wymagaj uwierzytelniania wieloskładnikowego na aktywnym przypisaniu lub aktywacji
- Wykonaj przeglądy dostępu
- Korzystanie z alertów i dzienników inspekcji

W tym artykule opisano, jak zapraszać gościa do organizacji i zarządzać dostępem do zasobów platformy Azure przy użyciu Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Kiedy zapraszasz Gości?

Oto kilka przykładowych scenariuszy, które mogą zapraszać Gości do organizacji:

- Zezwól zewnętrznemu dostawcy samoobsługowego, który ma dostęp do zasobów platformy Azure dla projektu i ma tylko konto e-mail.
- Zezwól partnerowi zewnętrznemu w dużej organizacji korzystającej z Active Directory Federation Services lokalnych w celu uzyskania dostępu do aplikacji do wydatków.
- Zezwól inżynierom pomocy technicznej, które nie są w organizacji (np. pomocy technicznej firmy Microsoft), aby tymczasowo uzyskiwać dostęp do zasobów platformy Azure w celu rozwiązywania problemów.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak działa współpraca przy użyciu Gości B2B?

Gdy używasz współpracy B2B, możesz zaprosić użytkownika zewnętrznego do organizacji jako gościa. Gość pojawia się w organizacji, ale gość nie ma skojarzonych z nim poświadczeń. Za każdym razem, gdy gość ma zostać uwierzytelniony, musi zostać uwierzytelniony w swojej organizacji domowej, a nie w organizacji. Oznacza to, że jeśli gość nie ma już dostępu do swojej organizacji domowej, utraci również dostęp do swojej organizacji. Na przykład jeśli gość opuści organizację, automatycznie utraci dostęp do wszystkich zasobów, które zostały Ci udostępnione w usłudze Azure AD bez konieczności wykonywania żadnych czynności. Aby uzyskać więcej informacji na temat B2B, zobacz temat [co to jest dostęp gościa w Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagram przedstawiający sposób, w jaki użytkownik jest wyświetlany w katalogu, ale jest uwierzytelniany w katalogu macierzystym](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Sprawdź ustawienia współpracy gościa

Aby upewnić się, że możesz zapraszać Gości do organizacji, sprawdź ustawienia współpracy gościa.

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).

1. Kliknij **Azure Active Directory** > **Ustawienia użytkownika**.

1. Kliknij pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.

    ![Strona ustawień współpracy zewnętrznej przedstawiające ustawienia ograniczeń uprawnień, zaproszeń i współpracy](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Upewnij się, że **Administratorzy i użytkownicy w roli osoby zapraszające gościa mogą zaprosić** przełącznik ma ustawioną **wartość tak**.

## <a name="invite-a-guest-and-assign-a-role"></a>Zapraszanie gościa i przypisywanie roli

Korzystając z usługi PIM, możesz zaprosić gościa i uczynić je uprawnionymi do roli zasobów platformy Azure, tak jak w przypadku użytkownika będącego członkiem.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) lub [administratora](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) .

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **zasoby platformy Azure**.

1. Użyj **filtru zasobów** , aby odfiltrować listę zarządzanych zasobów.

1. Kliknij zasób, którym chcesz zarządzać, na przykład zasób, grupę zasobów, subskrypcję lub grupę zarządzania.

    Należy ustawić zakres tylko na potrzeby gościa.

1. W obszarze Zarządzanie kliknij pozycję **role** , aby wyświetlić listę ról dla zasobów platformy Azure.

    ![Lista ról zasobów platformy Azure zawierająca liczbę aktywnych użytkowników i kwalifikujących się](./media/pim-resource-roles-external-users/resources-roles.png)

1. Kliknij minimalną rolę wymaganą przez użytkownika.

    ![Strona wybranej roli z listą bieżących członków tej roli](./media/pim-resource-roles-external-users/selected-role.png)

1. Na stronie Rola kliknij przycisk **Dodaj członka** , aby otworzyć okienko nowe przypisanie.

1. Kliknij pozycję **Wybierz członka lub grupę**.

    ![Nowe przypisanie — Wybierz okienko elementu członkowskiego lub grupy z listą użytkowników i grup wraz z opcją zaproszenia](./media/pim-resource-roles-external-users/select-member-group.png)

1. Aby zaprosić gościa, kliknij pozycję **Zaproś**.

    ![Zapraszanie strony Gościa z polami do wprowadzania adresu e-mail i określania wiadomości osobistej](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po wybraniu gościa kliknij pozycję **Zaproś**.

    Gość powinien zostać dodany jako wybrany element członkowski.

1. W okienku **Wybierz członka lub grupę** kliknij pozycję **Wybierz**.

1. W okienku **Ustawienia członkostwa** wybierz typ przypisania i czas trwania.

    ![Strona Ustawienia nowej przynależności do przypisania z opcjami określającymi typ przypisania, datę początkową i datę końcową](./media/pim-resource-roles-external-users/membership-settings.png)

1. Aby zakończyć przypisanie, kliknij przycisk **gotowe** , a następnie **Dodaj**.

    Przypisanie roli gościa zostanie wyświetlone na liście ról.

    ![Strona roli](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktywuj rolę jako gościa

Jako użytkownik zewnętrzny najpierw musisz zaakceptować zaproszenie do organizacji usługi Azure AD i ewentualnie aktywować rolę.

1. Otwórz wiadomość e-mail z zaproszeniem. Wiadomość e-mail będzie wyglądać podobnie do poniższego.

    ![Wyślij wiadomość e-mail z zaproszeniem z nazwą katalogu, wiadomością osobistą i linkiem wprowadzającym](./media/pim-resource-roles-external-users/email-invite.png)

1. Kliknij link **wprowadzenie** w wiadomości e-mail.

1. Po przejrzeniu uprawnień kliknij przycisk **Akceptuj**.

    ![Przeglądanie strony uprawnień w przeglądarce z listą uprawnień, które organizacja chce przejrzeć](./media/pim-resource-roles-external-users/invite-accept.png)

1. Może zostać wyświetlony monit o zaakceptowanie warunków użytkowania i określenie, czy użytkownik ma pozostać zalogowany.

    Zostanie otwarty Azure Portal. Jeśli masz uprawnienia tylko do roli, nie będziesz mieć dostępu do zasobów.

1. Aby aktywować rolę, Otwórz wiadomość e-mail przy użyciu linku Aktywuj rolę. Wiadomość e-mail będzie wyglądać podobnie do poniższego.

    ![Wiadomość e-mail z programu PIM wskazująca, że kwalifikujesz się do roli przy użyciu linku Aktywuj rolę](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Kliknij pozycję **Aktywuj rolę** , aby otworzyć odpowiednie role w programie PIM.

    ![Strona Moje role w usłudze PIM z listą uprawnionych ról](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. W obszarze Akcja kliknij link **Uaktywnij** .

    W zależności od ustawień roli należy określić kilka informacji w celu aktywowania roli.

1. Po określeniu ustawień dla roli kliknij pozycję **Aktywuj** , aby aktywować rolę.

    ![Aktywuj zakres i opcje wyświetlania strony, aby określić czas rozpoczęcia, czas trwania i powód](./media/pim-resource-roles-external-users/activate-role.png)

    Jeśli administrator nie musi zatwierdzić Twojego żądania, powinien mieć dostęp do określonych zasobów.

## <a name="view-activity-for-a-guest"></a>Wyświetl działania gościa

Podobnie jak w przypadku użytkownika będącego członkiem, można wyświetlić dzienniki inspekcji, aby śledzić działania Gości.

1. Jako administrator Otwórz program PIM i wybierz zasób, który został udostępniony.

1. Kliknij pozycję **Inspekcja zasobów** , aby wyświetlić działanie tego zasobu. Poniżej przedstawiono przykład działania dotyczącego grupy zasobów.

    ![Zasoby platformy Azure — na stronie Inspekcja zasobów na liście czas, Obiekt żądający i akcja](./media/pim-resource-roles-external-users/audit-resource.png)

1. Aby wyświetlić działanie gościa, kliknij pozycję **Azure Active Directory** > **Użytkownicy** > nazwa gościa.

1. Kliknij pozycję **dzienniki inspekcji** , aby wyświetlić dzienniki inspekcji dla organizacji. W razie potrzeby można określić filtry.

    ![Dzienniki inspekcji katalogów wymieniają datę, cel, zainicjowane przez i działanie](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról administratorów usługi Azure AD w usłudze PIM](pim-how-to-add-role-to-user.md)
- [Co to jest dostęp gościa w Azure Active Directory B2B?](../b2b/what-is-b2b.md)
