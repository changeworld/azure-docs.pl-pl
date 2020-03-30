---
title: Przypisywanie ról zasobów platformy Azure do gości w usłudze PIM — Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak zaprosić zewnętrznych użytkowników-gości i przypisać role zasobów platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021937"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Zapraszanie użytkowników-gości i przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi

Użytkownicy-goście-goście usługi Azure Ad (Azure AD) są częścią funkcji współpracy między firmami (B2B) w usłudze Azure AD, dzięki czemu można zarządzać zewnętrznymi użytkownikami i dostawcami-gośćmi w usłudze Azure AD. Po połączeniu współpracy B2B z usługą Azure AD Privileged Identity Management (PIM) można rozszerzyć wymagania dotyczące zgodności i nadzoru dla gości. Na przykład można użyć tych funkcji zarządzania tożsamościami uprzywilejowanymi dla zadań tożsamości platformy Azure z gośćmi:

- Przypisywanie dostępu do określonych zasobów platformy Azure
- Włącz dostęp just-in-time
- Określanie czasu trwania i daty zakończenia przydziału
- Wymagaj uwierzytelniania wieloskładnikowego przy aktywnym przypisywanie lub aktywacja
- Wykonywanie recenzji dostępu
- Korzystanie z alertów i dzienników inspekcji

W tym artykule opisano sposób zapraszania gościa do organizacji i zarządzania dostępem do zasobów platformy Azure przy użyciu zarządzania tożsamościami uprzywilejowanymi.

## <a name="when-would-you-invite-guests"></a>Kiedy zaprosisz gości?

Oto kilka przykładów, kiedy możesz zaprosić gości do swojej organizacji:

- Zezwalaj zewnętrznemu dostawcy na własny rachunek, który ma tylko konto e-mail, aby uzyskać dostęp do zasobów platformy Azure dla projektu.
- Zezwalaj zewnętrznemu partnerowi w dużej organizacji korzystającej z lokalnych usług fedascyjnych Active Directory na dostęp do aplikacji wydatków.
- Zezwalaj inżynierom pomocy technicznej niew organizacji (na przykład pomocy technicznej firmy Microsoft) na tymczasowy dostęp do zasobu platformy Azure w celu rozwiązywania problemów.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak działa współpraca z gośćmi B2B?

Korzystając ze współpracy B2B, można zaprosić użytkownika zewnętrznego do organizacji jako gościa. Gość może być zarządzany jako użytkownik w organizacji, ale gość musi być uwierzytelniony w organizacji domowej, a nie w organizacji usługi Azure AD. Oznacza to, że jeśli gość nie ma już dostępu do swojej organizacji macierzystej, utraci również dostęp do organizacji. Na przykład jeśli gość opuści swoją organizację, automatycznie utraci dostęp do wszystkich zasobów udostępnionych im w usłudze Azure AD bez konieczności wykonywania jakichkolwiek działań. Aby uzyskać więcej informacji na temat współpracy B2B, zobacz [Co to jest dostęp użytkownika-gościa w usłudze Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagram przedstawiający sposób uwierzytelniania użytkownika-gościa w katalogu domowym](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Sprawdzanie ustawień współpracy gościa

Aby upewnić się, że możesz zapraszać gości do swojej organizacji, sprawdź ustawienia współpracy gości.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

1. Wybierz**ustawienia użytkownika**usługi Azure **Active Directory** > .

1. Wybierz **pozycję Zarządzaj ustawieniami współpracy zewnętrznej**.

    ![Strona ustawień współpracy zewnętrznej z ustawieniami ograniczeń uprawnień, zaproszenia i ograniczenia współpracy](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Upewnij się, że **administratorzy i użytkownicy w roli osoby zapraszania gościa mogą zapraszać** przełącznik jest ustawiony na **Tak**.

## <a name="invite-a-guest-and-assign-a-role"></a>Zapraszanie gościa i przypisywanie roli

Za pomocą uprzywilejowanego zarządzania tożsamościami można zaprosić gościa i sprawić, by kwalifikował się do roli zasobów platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest członkiem [administratora ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) lub [administratora użytkownika.](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **zasoby platformy Azure**.

1. Filtr **Zasobów** służy do filtrowania listy zasobów zarządzanych.

1. Wybierz zasób, który chcesz zarządzać, taki jak zasób, grupa zasobów, subskrypcja lub grupa zarządzania.

    Należy ustawić zakres tylko to, czego potrzebuje gość.

1. W obszarze Zarządzanie wybierz pozycję **Role,** aby wyświetlić listę ról zasobów platformy Azure.

    ![Lista ról zasobów platformy Azure z liczbą użytkowników, którzy są aktywni i uprawnieni](./media/pim-resource-roles-external-users/resources-roles.png)

1. Wybierz minimalną rolę, której użytkownik będzie potrzebował.

    ![Wybrana strona roli z listą bieżących członków tej roli](./media/pim-resource-roles-external-users/selected-role.png)

1. Na stronie roli wybierz pozycję **Dodaj członka,** aby otworzyć okienko Nowe przypisanie.

1. Kliknij **pozycję Wybierz członka lub grupę**.

    ![Nowe przypisanie — wybieranie okienka członka lub grupy z listą użytkowników i grup wraz z opcją Zaproś](./media/pim-resource-roles-external-users/select-member-group.png)

1. Aby zaprosić gościa, kliknij przycisk **Zaproś**.

    ![Zapraszanie strony gościa z polami do wprowadzenia adresu e-mail i określenia wiadomości osobistej](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po wybraniu gościa kliknij przycisk **Zaproś**.

    Gość powinien zostać dodany jako wybrany element członkowski.

1. W okienku **Wybierz członka lub grupę** kliknij pozycję **Wybierz**.

1. W okienku **Ustawienia członkostwa** wybierz typ i czas trwania przypisania.

    ![Nowe przypisanie — strona Ustawienia członkostwa z opcjami określającymi typ przydziału, datę rozpoczęcia i datę zakończenia](./media/pim-resource-roles-external-users/membership-settings.png)

1. Aby ukończyć przypisanie, wybierz pozycję **Gotowe,** a następnie **dodaj**.

    Przypisanie roli gościa pojawi się na liście ról.

    ![Strona roli z wyszczególnienie gościa jako kwalifikującego się](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktywowanie roli gościa

Jeśli jesteś użytkownikiem zewnętrznym, musisz zaakceptować zaproszenie do bycia gościem w organizacji usługi Azure AD i ewentualnie aktywować przypisanie roli.

1. Otwórz wiadomość e-mail z zaproszeniem. Wiadomość e-mail będzie wyglądać podobnie do poniższej.

    ![Wyślij zaproszenie pocztą e-mail z nazwą katalogu, wiadomością osobistą i linkiem Wprowadzenie](./media/pim-resource-roles-external-users/email-invite.png)

1. Wybierz link **Wprowadzenie** w wiadomości e-mail.

1. Po przejrzeniu uprawnień kliknij przycisk **Zaakceptuj**.

    ![Przeglądanie strony uprawnień w przeglądarce z listą uprawnień, które organizacja chciałaby przejrzeć](./media/pim-resource-roles-external-users/invite-accept.png)

1. Może zostaćsz poproszony o zaakceptowanie warunków użytkowania i określenie, czy chcesz pozostać zalogowanym. W witrynie Azure portal, jeśli *kwalifikujesz się do* roli, nie będziesz jeszcze mieć dostępu do zasobów.

1. Aby aktywować przypisanie roli, otwórz wiadomość e-mail za pomocą linku aktywuj rolę. Wiadomość e-mail będzie wyglądać podobnie do poniższej.

    ![Wiadomość e-mail z informacją, że kwalifikujesz się do otrzymania roli z linkiem Aktywuj rolę](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Wybierz **pozycję Aktywuj rolę,** aby otworzyć kwalifikujące się role w zarządzania tożsamościami uprzywilejowanymi.

    ![Strona Moje role w zarządzania tożsamościami uprzywilejowanymi z listą kwalifikujących się ról](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. W obszarze Akcja wybierz łącze **Aktywuj.**

    W zależności od ustawień roli należy określić pewne informacje, aby aktywować rolę.

1. Po określeniu ustawień roli kliknij przycisk **Aktywuj,** aby aktywować rolę.

    ![Aktywowanie zakresu i opcji wyświetlania listy stron w celu określenia czasu rozpoczęcia, czasu trwania i przyczyny](./media/pim-resource-roles-external-users/activate-role.png)

    O ile administrator nie jest zobowiązany do zatwierdzenia żądania, powinieneś mieć dostęp do określonych zasobów.

## <a name="view-activity-for-a-guest"></a>Wyświetlanie aktywności dla gościa

Można przeglądać dzienniki inspekcji, aby śledzić, co robią goście.

1. Jako administrator otwórz zarządzanie tożsamościami uprzywilejowanymi i wybierz udostępniony zasób.

1. Wybierz **inspekcję zasobów,** aby wyświetlić działanie dla tego zasobu. Poniżej przedstawiono przykład działania dla grupy zasobów.

    ![Zasoby platformy Azure — strona inspekcji zasobów zawierająca listę czasu, żądacza i akcji](./media/pim-resource-roles-external-users/audit-resource.png)

1. Aby wyświetlić działanie dla gościa, wybierz*nazwę gościa***użytkownicy** >  **usługi Azure Active Directory** > .

1. Wybierz **dzienniki inspekcji,** aby wyświetlić dzienniki inspekcji dla organizacji. W razie potrzeby można określić filtry.

    ![Dzienniki inspekcji katalogu lista data, cel, zainicjowane przez, i działania](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról administratora usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md)
- [Co to jest dostęp użytkownika gościa we współpracy usługi Azure AD B2B?](../b2b/what-is-b2b.md)
