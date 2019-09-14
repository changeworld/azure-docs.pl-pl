---
title: Zarządzanie dostępem do zasobów platformy Azure dla zewnętrznych użytkowników-Gości korzystających z funkcji RBAC | Microsoft Docs
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników zewnętrznych w organizacji przy użyciu kontroli dostępu opartej na rolach (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 09/12/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 12f4b0276074b6732cf57443f51ef5d867f205a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967272"
---
# <a name="manage-access-to-azure-resources-for-external-guest-users-using-rbac"></a>Zarządzanie dostępem do zasobów platformy Azure dla zewnętrznych użytkowników-Gości korzystających z funkcji RBAC

Kontrola dostępu oparta na rolach (RBAC) umożliwia lepsze zarządzanie zabezpieczeniami w dużych organizacjach i małych i średnich firm pracujących z zewnętrznymi współpracownikami, dostawcami lub osobami niezależnymi, które potrzebują dostępu do określonych zasobów w środowisku, ale nie musi to być cała infrastruktura ani żadne zakresy związane z rozliczeniami. Możesz użyć możliwości w [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) , aby współpracować z zewnętrznymi użytkownikami-gośćmi i można użyć RBAC, aby przyznać tylko uprawnienia wymagane przez użytkowników-Gości w danym środowisku.

## <a name="when-would-you-invite-guest-users"></a>Kiedy zapraszasz użytkowników-Gości?

Oto kilka przykładowych scenariuszy, które mogą zapraszać użytkowników-Gości do organizacji i udzielać uprawnień:

- Zezwól zewnętrznemu dostawcy samoobsługowego, który ma dostęp do zasobów platformy Azure dla projektu i ma tylko konto e-mail.
- Zezwól partnerowi zewnętrznemu na zarządzanie określonymi zasobami lub całą subskrypcją.
- Zezwól inżynierom pomocy technicznej, które nie są w organizacji (np. pomocy technicznej firmy Microsoft), aby tymczasowo uzyskiwać dostęp do zasobów platformy Azure w celu rozwiązywania problemów.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Różnice uprawnień między użytkownikami należącymi do członków i użytkownikami-Gośćmi

Natywni członkowie katalogu (Użytkownicy z członkami) mają różne uprawnienia niż użytkownicy zaproszeni z innego katalogu jako gość współpracy B2B (Goście). Na przykład użytkownik może odczytać prawie wszystkie informacje o katalogu, podczas gdy użytkownicy-Goście mają ograniczone uprawnienia do katalogu. Aby uzyskać więcej informacji na temat użytkowników-członków i użytkowników-Gości, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Dodawanie użytkownika-gościa do katalogu

Wykonaj następujące kroki, aby dodać użytkownika-gościa do katalogu przy użyciu strony Azure Active Directory.

1. Upewnij się, że ustawienia współpracy zewnętrznej Twojej organizacji zostały skonfigurowane tak, aby można było zapraszać Gości. Aby uzyskać więcej informacji, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać Gości](../active-directory/b2b/delegate-invitations.md).

1. W Azure Portal kliknij pozycję **Azure Active Directory** > **Użytkownicy** > **nowy użytkownik-Gość**.

    ![Nowa funkcja użytkownika-gościa w Azure Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Postępuj zgodnie z instrukcjami, aby dodać nowego użytkownika-gościa. Aby uzyskać więcej informacji, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory).

Po dodaniu użytkownika-gościa do katalogu można wysłać użytkownikowi gość bezpośredni link do udostępnionej aplikacji lub kliknąć adres URL wykupu w wiadomości e-mail z zaproszeniem.

![Wiadomość e-mail z zaproszeniem gościa](./media/role-assignments-external-users/invite-email.png)

Aby użytkownik-gość mógł uzyskać dostęp do katalogu, musi zakończyć proces zaproszenia.

![Uprawnienia do przeglądania zaproszenia użytkownika-gościa](./media/role-assignments-external-users/invite-review-permissions.png)

Aby uzyskać więcej informacji na temat procesu zaproszenia, zobacz [Azure Active Directory realizacji zaproszeń do współpracy B2B](../active-directory/b2b/redemption-experience.md).

## <a name="grant-access-to-a-guest-user"></a>Udzielanie dostępu użytkownikowi gościa

Aby udzielić dostępu w ramach RBAC, należy przypisać rolę. Aby udzielić dostępu użytkownikowi gościa, wykonaj [te same czynności](role-assignments-portal.md#add-a-role-assignment) co w przypadku użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Wykonaj następujące kroki, aby udzielić dostępu użytkownikowi gościa w różnych zakresach.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**.

1.  Wybierz zestaw zasobów, do których odnosi się ten dostęp, nazywany również zakresem. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij przycisk **kontrola dostępu (IAM)** .

    Poniższy zrzut ekranu przedstawia przykład bloku kontroli dostępu (IAM) dla grupy zasobów. Jeśli w tym miejscu wprowadzisz jakiekolwiek zmiany kontroli dostępu, zostaną one zastosowane tylko do grupy zasobów.

    ![Blok kontroli dostępu (IAM) dla grupy zasobów](./media/role-assignments-external-users/access-control-resource-group.png)

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli.

    Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

    ![Menu Dodaj](./media/role-assignments-external-users/add-menu.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz użytkownika-gościa. Jeśli nie widzisz użytkownika na liście, możesz wpisać w polu **Wybierz** , aby przeszukać katalog pod kątem nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-external-users/add-role-assignment.png)

1. Kliknij przycisk **Zapisz** , aby przypisać rolę w wybranym zakresie.

    ![Przypisanie roli dla współautora maszyny wirtualnej](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="grant-access-to-a-guest-user-not-yet-in-your-directory"></a>Udziel dostępu użytkownikowi gościa jeszcze nie w Twoim katalogu

Aby udzielić dostępu w ramach RBAC, należy przypisać rolę. Aby udzielić dostępu użytkownikowi gościa, wykonaj [te same czynności](role-assignments-portal.md#add-a-role-assignment) co w przypadku użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej.

Jeśli użytkownik-Gość nie znajduje się jeszcze w Twoim katalogu, możesz zaprosić użytkownika bezpośrednio z okienka Dodawanie przypisania roli.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**.

1.  Wybierz zestaw zasobów, do których odnosi się ten dostęp, nazywany również zakresem. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij przycisk **kontrola dostępu (IAM)** .

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli.

    ![Menu Dodaj](./media/role-assignments-external-users/add-menu.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Na liście **Wybierz** wpisz adres e-mail osoby, którą chcesz zaprosić, i wybierz tę osobę.

   ![Zaproś użytkownika-gościa w okienku Dodaj przypisanie roli](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Kliknij przycisk **Zapisz** , aby dodać użytkownika-gościa do katalogu, przypisać rolę i wysłać zaproszenie.

    Po kilku chwilach zobaczysz powiadomienie o przypisaniu roli i informacje o zaproszeniu.

    ![Przypisanie roli i powiadomienie zaproszonego użytkownika](./media/role-assignments-external-users/invited-user-notification.png)

1. Aby ręcznie zaprosić użytkownika-gościa, kliknij prawym przyciskiem myszy i skopiuj link do zaproszenia w powiadomieniu. Nie klikaj linku zaproszenia, ponieważ uruchamia proces zaproszenia.

    Link do zaproszenia będzie miał następujący format:

    `https://invitations.microsoft.com/redeem/...`

1. Wyślij link zaproszenia do użytkownika-gościa, aby zakończyć proces zaproszenia.

    Aby uzyskać więcej informacji na temat procesu zaproszenia, zobacz [Azure Active Directory realizacji zaproszeń do współpracy B2B](../active-directory/b2b/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Usuń użytkownika-gościa z katalogu

Przed usunięciem użytkownika-gościa z katalogu należy najpierw usunąć wszystkie przypisania ról dla tego użytkownika. Wykonaj następujące kroki, aby usunąć użytkownika-gościa z katalogu.

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, gdzie użytkownik-Gość ma przypisanie roli.

1. Kliknij kartę **przypisania ról** , aby wyświetlić wszystkie przypisania ról.

1. Na liście przypisań ról Dodaj znacznik wyboru obok użytkownika-gościa z przypisaniem roli, które chcesz usunąć.

   ![Usuń przypisanie roli](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Kliknij pozycję **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-external-users/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

1. Na pasku nawigacyjnym po lewej stronie kliknij pozycję **Azure Active Directory** > **Użytkownicy**.

1. Kliknij użytkownika-gościa, który chcesz usunąć.

1. Kliknij przycisk **Usuń**.

   ![Usuń użytkownika-gościa](./media/role-assignments-external-users/delete-guest-user.png)

1. W wyświetlonym komunikacie Usuń kliknij przycisk **tak**.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="guest-user-cannot-browse-the-directory"></a>Użytkownik-Gość nie może przeglądać katalogu

Użytkownicy-Goście mają ograniczone uprawnienia do katalogu. Na przykład użytkownicy-Goście nie mogą przeglądać katalogu i nie mogą wyszukiwać grup ani aplikacji. Aby uzyskać więcej informacji, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![Użytkownik-Gość nie może przeglądać użytkowników w katalogu](./media/role-assignments-external-users/directory-no-users.png)

Jeśli użytkownik-Gość potrzebuje dodatkowych uprawnień w katalogu, można przypisać rolę katalogu do użytkownika-gościa. Jeśli chcesz, aby użytkownik-Gość miał pełny dostęp do odczytu do katalogu, możesz dodać użytkownika-gościa do roli [czytelnicy katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md) w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Przyznawanie uprawnień użytkownikom z organizacji partnerskich w dzierżawie Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![Przypisywanie roli czytelnicy Directory](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Użytkownik-Gość nie może przeglądać użytkowników, grup ani nazw głównych usług w celu przypisania ról

Użytkownicy-Goście mają ograniczone uprawnienia do katalogu. Nawet jeśli użytkownik-Gość jest [właścicielem](built-in-roles.md#owner) w określonym zakresie, próba utworzenia przypisania roli w celu udzielenia innej osobie dostępu nie może przeglądać listy użytkowników, grup ani nazw podmiotów usługi.

![Użytkownik-Gość nie może przeglądać podmiotów zabezpieczeń w celu przypisania ról](./media/role-assignments-external-users/directory-no-browse.png)

Jeśli użytkownik-Gość wie o dokładnej nazwie logowania w katalogu, może udzielić dostępu. Jeśli chcesz, aby użytkownik-Gość miał pełny dostęp do odczytu do katalogu, możesz dodać użytkownika-gościa do roli [czytelnicy katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md) w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Przyznawanie uprawnień użytkownikom z organizacji partnerskich w dzierżawie Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Użytkownik-Gość nie może zarejestrować aplikacji ani utworzyć jednostek usługi

Użytkownicy-Goście mają ograniczone uprawnienia do katalogu. Jeśli użytkownik-Gość musi mieć możliwość rejestrowania aplikacji lub tworzenia jednostek usługi, można dodać użytkownika-gościa do roli [Deweloper aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md) w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Przyznawanie uprawnień użytkownikom z organizacji partnerskich w dzierżawie Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![Użytkownik-Gość nie może zarejestrować aplikacji](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Użytkownik-Gość nie widzi nowego katalogu

Jeśli użytkownik-Gość uzyska dostęp do katalogu, ale nie zobaczy nowego katalogu wymienionego w Azure Portal podczas próby przełączenia się w okienku **katalogów i subskrypcji** , upewnij się, że użytkownik-Gość ukończy proces zaproszenia. Aby uzyskać więcej informacji na temat procesu zaproszenia, zobacz [Azure Active Directory realizacji zaproszeń do współpracy B2B](../active-directory/b2b/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Użytkownik-Gość nie widzi zasobów

Jeśli użytkownik-Gość uzyska dostęp do katalogu, ale nie widzi zasobów, do których udzielono dostępu w Azure Portal, upewnij się, że użytkownik-Gość zaznaczył poprawny katalog. Użytkownik-gość może mieć dostęp do wielu katalogów. Aby przełączyć katalogi, w lewym górnym rogu kliknij pozycję **katalog + subskrypcja**, a następnie kliknij odpowiedni katalog.

![Okienko katalogów i subskrypcji w Azure Portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Następne kroki

- [Dodają użytkowników we współpracy B2B usługi Azure Active Directory w witrynie Azure portal](../active-directory/b2b/add-users-administrator.md)
- [Właściwości Azure Active Directory użytkownika współpracy B2B](../active-directory/b2b/user-properties.md)
- [Elementy wiadomości e-mail z zaproszeniem do współpracy B2B — Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)