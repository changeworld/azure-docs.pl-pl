---
title: Dodawanie lub usuwanie przypisań ról dla użytkowników zewnętrznych za pomocą funkcji RBAC i witryny Azure portal
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure użytkownikom zewnętrznym w organizacji przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC).
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245644"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Dodawanie lub usuwanie przypisań ról dla zewnętrznych użytkowników-gości korzystających z usługi Azure RBAC i witryny Azure portal

[Kontrola dostępu oparta na rolach platformy Azure (RBAC)](overview.md) umożliwia lepsze zarządzanie zabezpieczeniami dla dużych organizacji oraz dla małych i średnich firm współpracujących z zewnętrznymi współpracownikami, dostawcami lub freelancerami, którzy potrzebują dostępu do określonych zasobów w danym środowisku, ale niekoniecznie do całej infrastruktury lub dowolnych zakresów związanych z rozliczeniami. Można użyć możliwości w [usłudze Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) do współpracy z zewnętrznymi użytkownikami gości i można użyć RBAC, aby udzielić tylko uprawnienia, które użytkownicy-goście potrzebują w twoim środowisku.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnień, takich jak [Administrator dostępu do użytkownika](built-in-roles.md#user-access-administrator) lub [Właściciel](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Kiedy zaprosisz użytkowników-gości?

Oto kilka przykładowych scenariuszy, w których można zaprosić użytkowników-gości do organizacji i udzielić uprawnień:

- Zezwalaj zewnętrznemu dostawcy na własny rachunek, który ma tylko konto e-mail, aby uzyskać dostęp do zasobów platformy Azure dla projektu.
- Zezwalaj partnerowi zewnętrznemu na zarządzanie określonymi zasobami lub całą subskrypcją.
- Zezwalaj inżynierom pomocy technicznej niew organizacji (na przykład pomocy technicznej firmy Microsoft) na tymczasowy dostęp do zasobu platformy Azure w celu rozwiązywania problemów.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Różnice uprawnień między użytkownikami członkowskimi a użytkownikami-gośćmi

Natywnych członków katalogu (użytkowników elementów członkowskich) mają inne uprawnienia niż użytkownicy zaproszeni z innego katalogu jako gość współpracy B2B (użytkownicy-goście).Native members of a directory (member users) have different permissions than users invited from another directory as a B2B collaboration guest (guest users). Na przykład użytkownik członkowek może odczytać prawie wszystkie informacje o katalogu, podczas gdy użytkownicy-goście mają ograniczone uprawnienia katalogu. Aby uzyskać więcej informacji o użytkownikach elementów członkowskich i użytkownikach-gościach, zobacz [Jakie są domyślne uprawnienia użytkowników w usłudze Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Dodawanie użytkownika-gościa do katalogu

Wykonaj następujące kroki, aby dodać użytkownika-gościa do katalogu przy użyciu strony usługi Azure Active Directory.

1. Upewnij się, że ustawienia współpracy zewnętrznej w organizacji są skonfigurowane w taki sposób, że możesz zapraszać gości. Aby uzyskać więcej informacji, zobacz [Włączanie współpracy zewnętrznej B2B i zarządzanie osobami, które mogą zapraszać gości.](../active-directory/b2b/delegate-invitations.md)

1. W witrynie Azure portal kliknij pozycję**Użytkownicy** >  **usługi Azure Active Directory** > Nowy**użytkownik-gość**.

    ![Nowa funkcja użytkownika gościa w witrynie Azure portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Wykonaj kroki, aby dodać nowego użytkownika-gościa. Aby uzyskać więcej informacji, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory).

Po dodaniu użytkownika-gościa do katalogu można wysłać użytkownikowi-gościowi bezpośrednie łącze do udostępnionej aplikacji lub użytkownik-gość może kliknąć adres URL realizacji w wiadomości e-mail z zaproszeniem.

![Adres e-mail z zaproszeniem użytkownika-gościa](./media/role-assignments-external-users/invite-email.png)

Aby użytkownik-gość mógł uzyskać dostęp do katalogu, musi ukończyć proces zaproszenia.

![Uprawnienia do recenzji zaproszeń użytkownika gościa](./media/role-assignments-external-users/invite-review-permissions.png)

Aby uzyskać więcej informacji na temat procesu zaproszenia, zobacz [Realizacja zaproszenia do współpracy usługi Azure Active Directory B2B](../active-directory/b2b/redemption-experience.md).

## <a name="add-a-role-assignment-for-a-guest-user"></a>Dodawanie przypisania roli dla użytkownika-gościa

W RBAC, aby udzielić dostępu, należy przypisać rolę. Aby dodać przypisanie roli dla użytkownika-gościa, wykonaj [te same kroki,](role-assignments-portal.md#add-a-role-assignment) co w przypadku użytkownika członkowskiego, grupy, jednostki usługi lub tożsamości zarządzanej. Wykonaj następujące kroki, dodaj przypisanie roli dla użytkownika-gościa w różnych zakresach.

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi**.

1.  Wybierz zestaw zasobów, które ma zastosowanie do dostępu, znany również jako zakres. Na przykład można wybrać **grupy zarządzania,** subskrypcje, **grupy zasobów**lub **zasób.**

1. Kliknij określony zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

    Poniższy zrzut ekranu przedstawia przykład bloku kontroli dostępu (IAM) dla grupy zasobów. Jeśli w tym miejscu zostaną wprowadzone zmiany kontroli dostępu, będą one dotyczyć tylko grupy zasobów.

    ![Blok kontroli dostępu (IAM) dla grupy zasobów](./media/role-assignments-external-users/access-control-resource-group.png)

1. Kliknij kartę **Przypisania ról,** aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij **pozycję Dodaj** > **przypisanie roli Dodaj,** aby otworzyć okienko Dodawanie przypisania roli.

    Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

    ![Menu Dodaj](./media/role-assignments-external-users/add-menu.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Na liście **Wybierz** wybierz użytkownika-gościa. Jeśli nie widzisz użytkownika na liście, możesz wpisać pole **Wyboru,** aby przeszukać katalog w poszukiwaniu wyświetlanych nazw, adresów e-mail i identyfikatorów obiektów.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-external-users/add-role-assignment.png)

1. Kliknij **przycisk Zapisz,** aby przypisać rolę w wybranym zakresie.

    ![Przypisanie roli współautorowi maszyny wirtualnej](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Dodawanie przypisania roli dla użytkownika-gościa, który nie jest jeszcze w katalogu

Aby dodać przypisanie roli dla użytkownika-gościa, wykonaj [te same kroki,](role-assignments-portal.md#add-a-role-assignment) co w przypadku użytkownika członkowskiego, grupy, jednostki usługi lub tożsamości zarządzanej.

Jeśli użytkownik-gość nie znajduje się jeszcze w katalogu, można zaprosić użytkownika bezpośrednio z okienka Dodawanie przypisania roli.

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi**.

1.  Wybierz zestaw zasobów, które ma zastosowanie do dostępu, znany również jako zakres. Na przykład można wybrać **grupy zarządzania,** subskrypcje, **grupy zasobów**lub **zasób.**

1. Kliknij określony zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Przypisania ról,** aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij **pozycję Dodaj** > **przypisanie roli Dodaj,** aby otworzyć okienko Dodawanie przypisania roli.

    ![Menu Dodaj](./media/role-assignments-external-users/add-menu.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Na liście **Wybierz** wpisz adres e-mail osoby, którą chcesz zaprosić, i wybierz tę osobę.

   ![Zapraszanie użytkownika-gościa w okienku Dodawanie przypisania roli](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Kliknij **przycisk Zapisz,** aby dodać użytkownika-gościa do katalogu, przypisać rolę i wysłać zaproszenie.

    Po kilku chwilach zobaczysz powiadomienie o przypisaniu roli i informacje o zaproszeniu.

    ![Przypisywanie ról i powiadomienie zaproszonego użytkownika](./media/role-assignments-external-users/invited-user-notification.png)

1. Aby ręcznie zaprosić użytkownika-gościa, kliknij prawym przyciskiem myszy i skopiuj łącze zaproszenia w powiadomieniu. Nie klikaj łącza zaproszenia, ponieważ rozpoczyna on proces zaproszenia.

    Łącze zaproszenia będzie miało następujący format:

    `https://invitations.microsoft.com/redeem/...`

1. Wyślij link z zaproszeniem do użytkownika-gościa, aby zakończyć proces zaproszenia.

    Aby uzyskać więcej informacji na temat procesu zaproszenia, zobacz [Realizacja zaproszenia do współpracy usługi Azure Active Directory B2B](../active-directory/b2b/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Usuwanie użytkownika-gościa z katalogu

Przed usunięciem użytkownika-gościa z katalogu należy najpierw usunąć wszystkie przypisania ról dla tego użytkownika-gościa. Wykonaj następujące kroki, aby usunąć użytkownika-gościa z katalogu.

1. **Open Access control (IAM)** w zakresie, takim jak grupa zarządzania, subskrypcja, grupa zasobów lub zasób, w którym użytkownik-gość ma przypisanie roli.

1. Kliknij kartę **Przypisania ról,** aby wyświetlić wszystkie przypisania ról.

1. Na liście przypisań ról dodaj znacznik wyboru obok użytkownika-gościa z przypisaniem roli, które chcesz usunąć.

   ![Usuwanie przypisania roli](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Kliknij pozycję **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-external-users/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

1. Na lewym pasku nawigacyjnym kliknij pozycję**Użytkownicy** **usługi Azure Active Directory** > .

1. Kliknij użytkownika-gościa, którego chcesz usunąć.

1. Kliknij **pozycję Usuń**.

   ![Usuwanie użytkownika-gościa](./media/role-assignments-external-users/delete-guest-user.png)

1. W wyświetlonym komunikacie o usunięciu kliknij przycisk **Tak**.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="guest-user-cannot-browse-the-directory"></a>Użytkownik-gość nie może przeglądać katalogu

Użytkownicy-goście mają ograniczone uprawnienia do katalogów. Na przykład użytkownicy-goście nie mogą przeglądać katalogu i nie mogą wyszukiwać grup ani aplikacji. Aby uzyskać więcej informacji, zobacz [Jakie są domyślne uprawnienia użytkownika w usłudze Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![Użytkownik-gość nie może przeglądać użytkowników w katalogu](./media/role-assignments-external-users/directory-no-users.png)

Jeśli użytkownik-gość potrzebuje dodatkowych uprawnień w katalogu, można przypisać rolę katalogu do użytkownika-gościa. Jeśli naprawdę chcesz, aby użytkownik-gość miał pełny dostęp do odczytu do katalogu, możesz dodać użytkownika-gościa do roli [Czytniki katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md) w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Udzielanie uprawnień użytkownikom z organizacji partnerskich w dzierżawie usługi Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![Rola Przypisywanie czytników katalogów](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Użytkownik-gość nie może przeglądać użytkowników, grup ani podmiotów zabezpieczeń usługi w celu przypisania ról

Użytkownicy-goście mają ograniczone uprawnienia do katalogów. Nawet jeśli użytkownik-gość jest [właścicielem](built-in-roles.md#owner) w zakresie, jeśli spróbuje dodać przypisanie roli, aby udzielić komuś dostępu innej osobie, nie może przeglądać listy użytkowników, grup lub podmiotów usługi.

![Użytkownik-gość nie może przeglądać podmiotów zabezpieczeń w celu przypisania ról](./media/role-assignments-external-users/directory-no-browse.png)

Jeśli użytkownik-gość zna dokładną nazwę logowania w katalogu, może udzielić dostępu. Jeśli naprawdę chcesz, aby użytkownik-gość miał pełny dostęp do odczytu do katalogu, możesz dodać użytkownika-gościa do roli [Czytniki katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md) w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Udzielanie uprawnień użytkownikom z organizacji partnerskich w dzierżawie usługi Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Użytkownik-gość nie może rejestrować aplikacji ani tworzyć podmiotów świadczących usługi

Użytkownicy-goście mają ograniczone uprawnienia do katalogów. Jeśli użytkownik-gość musi mieć możliwość rejestrowania aplikacji lub tworzenia podmiotów usługi, można dodać użytkownika-gościa do roli [dewelopera aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md) w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Udzielanie uprawnień użytkownikom z organizacji partnerskich w dzierżawie usługi Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![Użytkownik-gość nie może rejestrować aplikacji](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Użytkownik-gość nie widzi nowego katalogu

Jeśli użytkownikowi-gościowi udzielono dostępu do katalogu, ale nie widzą nowego katalogu wymienionego w witrynie Azure portal podczas próby przełączenia w okienku **subskrypcji Katalog + subskrypcja,** upewnij się, że użytkownik-gość zakończył proces zaproszenia. Aby uzyskać więcej informacji na temat procesu zaproszenia, zobacz [Realizacja zaproszenia do współpracy usługi Azure Active Directory B2B](../active-directory/b2b/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Użytkownik-gość nie widzi zasobów

Jeśli użytkownikowi-gościowi udzielono dostępu do katalogu, ale nie widzą zasobów, do których udzielono im dostępu w witrynie Azure Portal, upewnij się, że użytkownik-gość wybrał poprawny katalog. Użytkownik-gość może mieć dostęp do wielu katalogów. Aby przełączyć katalogi, w lewym górnym rogu kliknij pozycję **Katalog + subskrypcja**, a następnie kliknij odpowiedni katalog.

![Okienko Katalogi + Subskrypcje w witrynie Azure portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](../active-directory/b2b/add-users-administrator.md)
- [Właściwości użytkownika współpracy B2B usługi Azure Active Directory](../active-directory/b2b/user-properties.md)
- [Elementy wiadomości e-mail z zaproszeniem do współpracy B2B — usługa Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Dodawanie użytkownika-gościa jako współadministratora](classic-administrators.md#add-a-guest-user-as-a-co-administrator)