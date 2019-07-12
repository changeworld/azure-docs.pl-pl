---
title: 'Samouczek: Konfigurowanie usługi Smartsheet do automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników w usłudze Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670931"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Smartsheet do automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Smartsheet i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w usłudze Smartsheet.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawca usługi Smartsheet](https://www.smartsheet.com/pricing)
* Konto użytkownika w ramach planu Enterprise Premier lub Smartsheet Enterprise z uprawnieniami administratora systemu.

## <a name="assign-users-to-smartsheet"></a>Przypisywanie użytkowników do usługi Smartsheet

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do narzędzia Smartsheet. Po decyzję, należy przypisać użytkowników i/lub grup do narzędzia Smartsheet, zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Smartsheet

* Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisane do usługi Smartsheet do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do usługi Smartsheet, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

* Aby zapewnić parzystości w przypisaniach ról użytkownika między Smartsheet i Azure AD, zalecane jest korzystanie z tego samego przypisań ról w z pełną listą użytkowników usługi Smartsheet. Aby pobrać tę listę użytkowników z usługi Smartsheet, przejdź do **konto administratora > Zarządzanie użytkownikami > więcej akcji > Pobierz listę użytkowników (csv)** .

* Dostęp do pewnych funkcji w aplikacji, usługi Smartsheet wymaga od użytkownika mieć wiele ról. Aby dowiedzieć się więcej na temat typów użytkowników i uprawnień w usłudze Smartsheet, przejdź do [typy użytkowników i uprawnień](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Jeśli użytkownik ma wiele ról przypisany w usłudze Smartsheet, możesz **musi** upewnij się, że te przypisania roli są replikowane w usłudze Azure AD, aby uniknąć scenariusz, w którym użytkownicy mogłoby spowodować utratę dostępu do obiektów usługi Smartsheet trwale. Każdy szczególnej roli w usłudze Smartsheet **musi** można przypisać do innej grupy w usłudze Azure AD. Użytkownik **musi** , a następnie można dodać do każdej z grup, odpowiadający żądanego ról. 

## <a name="set-up-smartsheet-for-provisioning"></a>Konfigurowanie usługi Smartsheet do inicjowania obsługi

Przed rozpoczęciem konfigurowania usługi Smartsheet dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, należy włączyć Standard SCIM aprowizację w narzędziu Smartsheet.

1. Zaloguj się jako **SysAdmin** w **[Smartsheet portal](https://app.smartsheet.com/b/home)** i przejdź do **administrator konta**.

    ![Administrator konta Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Przejdź do **środki kontroli bezpieczeństwa > użytkownika automatycznego inicjowania obsługi administracyjnej > Edytuj**.

    ![Zabezpieczenia usługi Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Dodaj i Zweryfikuj domen poczty e-mail dla użytkowników, które mają być aprowizacji z usługi Azure AD w usłudze Smartsheet. Wybierz **nie włączono** upewnij się, że wszystkich akcji aprowizacji pochodzić tylko z usługi Azure AD i również upewnij się, że na liście użytkowników usługi Smartsheet w synchronizacji z przydziałami usługi Azure AD.

    ![Aprowizacja użytkowników usługi Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Po zakończeniu sprawdzania poprawności, należy aktywować domeny. 

    ![Usługa Smartsheet aktywować domeny](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Generowanie **klucz tajny tokenu** wymagane do skonfigurowania automatycznej aprowizacji użytkowników z usługą Azure AD, przechodząc do **aplikacji i integracji**.

    ![Zainstaluj narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Wybierz **dostęp do interfejsu API**. Kliknij przycisk **Wygeneruj nowy token dostępu**.

    ![Zainstaluj narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Zdefiniuj nazwę tokenu dostępu do interfejsu API. Kliknij przycisk **OK**.

    ![Zainstaluj narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Skopiuj Token dostępu do interfejsu API i zapisz go, ponieważ jest to jedyny raz, można go wyświetlić. Jest to wymagane **klucz tajny tokenu** pola w usłudze Azure AD.

    ![Token usługi Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Dodawanie usługi Smartsheet za pomocą galerii

Aby skonfigurować usługi Smartsheet dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, należy dodać Smartsheet za pomocą galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Smartsheet**, wybierz opcję **Smartsheet** w panelu wyników. 

    ![Usługa Smartsheet na liście wyników](common/search-new-app.png)

5. Wybierz **bezpłatnie Smartsheet** przycisk, co spowoduje przekierowanie do strony logowania w usłudze Smartsheet. 

    ![Dodawanie usługi Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Podobnie jak Smartsheet OpenIDConnect aplikacji, wybierz można zalogować się do usługi Smartsheet za pomocą swojego konta służbowego Microsoft.

    ![Logowania OIDC narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Po pomyślnym uwierzytelnieniu zaakceptować monit o wyrażenie zgody na strona zgody użytkownika. Aplikacja zostanie następnie automatycznie dodana do dzierżawy, a następnie nastąpi przekierowanie do Twojego konta Smartsheet.

    ![Usługa Smartsheet OIDc zgody](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Konfigurowanie automatycznej aprowizacji użytkowników do usługi Smartsheet 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w usłudze Smartsheet oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla usługi Smartsheet w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Smartsheet**.

    ![Link Smartsheet, na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych `https://scim.smartsheet.com/v2/` w **adres URL dzierżawy**. Wartość, która pobierane i zapisany wcześniej z usługi Smartsheet w danych wejściowych **klucz tajny tokenu**. Kliknij przycisk **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z usługi Smartsheet. Jeśli połączenie nie powiedzie się, upewnij się, że na Twoim koncie Smartsheet, ma uprawnienia administratora systemu i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij polecenie **Zapisz**.

8. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do usługi Smartsheet**.

    ![Mapowania użytkownika narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do usługi Smartsheet w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Smartsheet dla operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkownika narzędzia Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługi Azure AD, inicjowania obsługi usługi Smartsheet, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na Smartsheet, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

13. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi w narzędziu Smartsheet.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Usługa Smartsheet nie obsługuje usuwania nietrwałego. Gdy użytkownik **active** atrybut jest ustawiony na wartość False, Smartsheet trwale usuwa użytkownika.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
