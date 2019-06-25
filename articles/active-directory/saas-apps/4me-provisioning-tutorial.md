---
title: 'Samouczek: Konfigurowanie 4me dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do 4me.
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
ms.date: 06/3/2019
ms.author: zchia
ms.openlocfilehash: 0cfd760eb9d631bf6ab098afef0ef9b66c92cfa6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168157"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie 4me dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w 4me i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD do automatycznej aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu 4me.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa 4me](https://www.4me.com/trial/)
* Konto użytkownika w 4me z uprawnieniami administratora.

## <a name="add-4me-from-the-gallery"></a>Dodaj 4me z galerii

Przed skonfigurowaniem 4me dla automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać 4me z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać 4me z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **4me**, wybierz opcję **4me** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja 4me na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Przypisywanie użytkowników do 4me

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do 4me. Po decyzję, możesz przypisać użytkownikom i/lub grup 4me zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Ważne wskazówki dotyczące przypisywania użytkowników do 4me

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do 4me do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do 4me, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Konfigurowanie automatycznej aprowizacji użytkowników do 4me 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grup w oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD 4me.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla 4me, zgodnie z instrukcjami podanymi w [4me pojedynczego logowania jednokrotnego samouczek](4me-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla 4me w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **4me**.

    ![Link 4me na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. Aby pobrać **adres URL dzierżawy** i **klucz tajny tokenu** konta 4me, postępuj zgodnie z tym przewodnikiem zgodnie z opisem w kroku 6.

6. Zaloguj się do konsoli administratora programu 4me. Przejdź do **ustawienia**.

    ![Ustawienia 4me](media/4me-provisioning-tutorial/4me01.png)

    Wpisz **aplikacje** na pasku wyszukiwania.

    ![aplikacje 4me](media/4me-provisioning-tutorial/4me02.png)

    Otwórz **Standard SCIM** listę rozwijaną, aby pobrać klucz tajny tokenu i punktu końcowego Standard SCIM.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z 4me. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto 4me ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do 4me**.

    ![Mapowania użytkownika 4me](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD 4me w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w 4me operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Mapowania użytkownika 4me](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do 4me**.

    ![Mapowania użytkownika 4me](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD 4me w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w 4me operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Mapowania grup 4me](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD, usługi dla 4me inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

16. Definiowanie użytkowników i/lub grup, które chcesz, aby obsługiwać je na 4me, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na 4me aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* 4me ma inny adres URL punktu końcowego Standard SCIM dla środowisk testowych i produkcyjnych. Pierwsza kończy się **.qa** podczas ostatniego kończy się znakiem **.com**
* 4me wygenerowany klucz tajny tokeny mają datę wygaśnięcia miesiąca z generacji.
* nie obsługuje 4me **Usuń** operacji

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)