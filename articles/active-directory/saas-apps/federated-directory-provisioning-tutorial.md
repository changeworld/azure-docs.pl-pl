---
title: 'Samouczek: Konfigurowanie federacyjnego katalogu dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników federacyjnych katalogu.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 19f5690a6852161abce2565a8c4a52ce86ff5187
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840591"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie federacyjnego katalogu dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w katalogu federacyjnych i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznego aprowizowania lub cofania aprowizacji użytkowników i/lub grup do katalogu federacyjnych.

> [!NOTE]
>  W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [A federacyjnych katalogu](https://www.federated.directory/pricing).
* Konto użytkownika w katalogu Sfederowane z uprawnieniami administratora.

## <a name="assign-users-to-federated-directory"></a>Przypisywanie użytkowników do katalogu federacyjnego
Usługa Azure Active Directory używa koncepcji o nazwie przypisania, aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do katalogu usługi federacyjnej. Po decyzję, należy przypisać użytkowników i/lub grup do katalogu usługi federacyjnej, zgodnie z instrukcjami w tym miejscu:

 * [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Ważne wskazówki dotyczące przypisywania użytkowników do katalogu federacyjnych
 * Zalecane jest, pojedynczego użytkownika usługi Azure AD jest przypisane do katalogu federacyjnych do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Gdy przypisanie użytkownika do katalogu federacyjnych, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z rolą domyślnego dostępu są wykluczane z inicjowania obsługi administracyjnej.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Konfigurowanie federacyjnego katalogu do inicjowania obsługi

Przed rozpoczęciem konfigurowania federacyjnych katalogu dla automatycznej aprowizacji użytkowników z usługą Azure AD, należy włączyć Standard SCIM aprowizację w katalogu usługi federacyjnej.

1. Zaloguj się do Twojej [federacyjnych konsoli administracyjnej katalogu](https://federated.directory/of)

    ![Samouczek usługi federacyjnej katalogu](media/federated-directory-provisioning-tutorial/companyname.png)

2. Przejdź do **katalogi > katalogów użytkownika** i wybrać dzierżawy. 

    ![directory federacyjnego](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Aby wygenerować token elementu nośnego trwałe, przejdź do **klucze katalogu > Utwórz nowy klucz.** 

    ![directory federacyjnego](media/federated-directory-provisioning-tutorial/federated01.png)

4. Utwórz klucz katalogu. 

    ![directory federacyjnego](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Kopiuj **Token dostępu** wartość. Ta wartość zostanie wprowadzona w **klucz tajny tokenu** pola na karcie Aprowizacja aplikacji federacyjnych katalog w witrynie Azure portal. 

    ![directory federacyjnego](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Dodaj katalog Sfederowane za pomocą galerii

Aby skonfigurować federacyjne katalogu dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, należy dodać Directory Sfederowana z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Directory Sfederowana z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Directory Sfederowana**, wybierz opcję **Directory Sfederowana** w panelu wyników.

    ![Federacyjne katalogu na liście wyników](common/search-new-app.png)

5. Przejdź do **adresu URL** wyróżniono poniżej w osobnej przeglądarce. 

    ![directory federacyjnego](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Kliknij przycisk **ZALOGUJ**.

    ![directory federacyjnego](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Podobnie jak federacyjnych katalogu aplikacji OpenIDConnect, wybierz można zalogować się do katalogu federacyjnych, za pomocą swojego konta służbowego Microsoft.
    
    ![directory federacyjnego](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Po pomyślnym uwierzytelnieniu zaakceptować monit o wyrażenie zgody na strona zgody użytkownika. Aplikacja zostanie następnie automatycznie dodana do dzierżawy, a następnie nastąpi przekierowanie do konta usługi federacyjnej Directory.

    ![directory federacyjnego Dodaj Standard SCIM](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurowanie automatycznej aprowizacji użytkowników federacyjnych katalogu 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grup w usłudze federacyjnej Directory oparte na użytkownika i/lub przypisania grupy w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Aby skonfigurować automatycznej aprowizacji użytkowników federacyjnych katalogu w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Directory Sfederowana**.

    ![Link katalogu federacyjnych na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych `https://api.federated.directory/v2/` w adresie URL dzierżawy. Wprowadź wartość, pobierane przez użytkownika, który wcześniej zapisany z katalogu federacyjnych **klucz tajny tokenu**. Kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD connect można federacyjnych katalogu. Jeśli połączenie nie powiedzie się, upewnij się, że konta usługi federacyjnej Directory ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + tokenu](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij polecenie **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do katalogu federacyjnych**.

    ![Samouczek usługi federacyjnej katalogu](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do katalogu federacyjnych w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w katalogu federacyjnych dla operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Samouczek usługi federacyjnej katalogu](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD, usługi federacyjne katalogu inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

14. Zdefiniować użytkowników i/lub grup, które chcesz aprowizować federacyjnych katalogu, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, usługi federacyjne katalogu inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowania po aprowizacji konta użytkowników](../manage-apps/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)