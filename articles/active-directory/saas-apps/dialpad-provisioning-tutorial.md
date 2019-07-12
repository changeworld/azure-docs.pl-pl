---
title: 'Samouczek: Konfigurowanie Dialpad dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Dialpad.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611778"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Dialpad dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Dialpad i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Dialpad.

> [!NOTE]
>  W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawy Dialpad](https://www.dialpad.com/pricing/).
* Konto użytkownika w Dialpad z uprawnieniami administratora.

## <a name="assign-users-to-dialpad"></a>Przypisywanie użytkowników do Dialpad
Usługa Azure Active Directory używa koncepcji o nazwie przypisania, aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Dialpad. Po decyzję, możesz przypisać użytkownikom i/lub grup Dialpad zgodnie z instrukcjami w tym miejscu:
 
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Dialpad

 * Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Dialpad do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Dialpad, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z rolą domyślnego dostępu są wykluczane z inicjowania obsługi administracyjnej.

## <a name="setup-dialpad-for-provisioning"></a>Konfigurowanie Dialpad do inicjowania obsługi

Przed skonfigurowaniem Dialpad dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, należy pobrać niektóre informacje o udostępnianiu z Dialpad.

1. Zaloguj się do Twojej [konsoli administracyjnej Dialpad](https://dialpadbeta.com/login) i wybierz **ustawienia administratora**. Upewnij się, że **mojej firmy** wybrania z listy rozwijanej. Przejdź do **uwierzytelniania > klucze interfejsu API**.

    ![Dialpad Dodaj Standard SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Wygeneruj nowy klucz, klikając **Dodaj klucz** i konfigurowania właściwości wpisu tajnego token.

    ![Dialpad Dodaj Standard SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad Dodaj Standard SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Kliknij przycisk **kliknij, aby wyświetlić wartość** przycisk ostatnio utworzone klucz interfejsu API, a następnie skopiuj wartość wyświetlana. Ta wartość zostanie wprowadzona w **klucz tajny tokenu** pola na karcie Aprowizacja Dialpad aplikacji w witrynie Azure portal. 

    ![Dialpad Utwórz Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dodaj Dialpad z galerii

W przypadku konfigurowania Dialpad dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać Dialpad z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Dialpad z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Dialpad**, wybierz opcję **Dialpad** w panelu wyników.
    ![Dialpad na liście wyników](common/search-new-app.png)

5. Przejdź do **adresu URL** wyróżniono poniżej w osobnej przeglądarce. 

    ![Dialpad Dodaj Standard SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. W prawym górnym rogu wybierz **logowanie > Użyj Dialpad online**.

    ![Dialpad Dodaj Standard SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Podobnie jak Dialpad OpenIDConnect aplikacji, wybierz można zalogować się do Dialpad przy użyciu swojego konta służbowego Microsoft.

    ![Dialpad Dodaj Standard SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Po pomyślnym uwierzytelnieniu zaakceptować monit o wyrażenie zgody na strona zgody użytkownika. Aplikacja zostanie następnie automatycznie dodana do dzierżawy, a następnie nastąpi przekierowanie do swojego konta Dialpad.

    ![Dialpad Dodaj Standard SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Dialpad

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Dialpad oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Dialpad w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Dialpad**.

    ![Link Dialpad na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych `https://dialpad.com/scim` w **adres URL dzierżawy**. Wartość, która pobierane i zapisane wcześniej Dialpad w danych wejściowych **klucz tajny tokenu**. Kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Dialpad. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Dialpad ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + tokenu](common/provisioning-testconnection-tenanturltoken.png)

6. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij polecenie **Zapisz**.

8. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Dialpad**.

    ![Mapowania użytkownika Dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Dialpad w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Dialpad operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkownika Dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługi Azure AD, usługi dla Dialpad inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

12. Definiowanie użytkowników i/lub grup, które chcesz, aby obsługiwać je na Dialpad, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

13. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Dialpad aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowania po aprowizacji konta użytkowników](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Ograniczenia łącznika
* Dialpad nie obsługuje obecnie zmienia nazwę grupy. Oznacza to, że wszelkie zmiany **displayName** grupy w usłudze Azure AD nie zostanie zaktualizowany i zostaną uwzględnione w Dialpad.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
