---
title: 'Samouczek: Konfigurowanie dynamicznego sygnał dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do sygnałów dynamicznych.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: f79bc083105f997b08f7cfa6f8e08a8f4f455455
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65470536"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie dynamicznego sygnał dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w sygnałów dynamicznych i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznego aprowizowania lub cofania aprowizacji użytkowników i/lub grup w celu dynamicznego sygnału.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawy sygnałów dynamicznych](https://dynamicsignal.com/)
* Konto użytkownika w sygnałów dynamicznych z uprawnieniami administratora.

## <a name="add-dynamic-signal-from-the-gallery"></a>Dodaj sygnałów dynamicznych z galerii

Przed skonfigurowaniem sygnałów dynamicznych dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać sygnałów dynamicznych z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać sygnałów dynamicznych z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **sygnałów dynamicznych**, wybierz opcję **sygnałów dynamicznych** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja Dynamic Signal na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Przypisywanie użytkowników do sygnałów dynamicznych

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do sygnałów dynamicznych. Po decyzję, należy przypisać użytkowników i/lub grup dynamicznych sygnału, zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Ważne wskazówki dotyczące przypisywania użytkowników do sygnałów dynamicznych

* Zalecane jest, że jeden użytkownik usługi Azure AD jest przypisany do sygnałów dynamicznych, aby przetestować automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do sygnałów dynamicznych, musisz wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Konfigurowanie automatycznej aprowizacji użytkowników do dynamicznego sygnału 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grup w sygnałów dynamicznych, w oparciu o użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowanie jednokrotne dla dynamicznych sygnału, postępując zgodnie z instrukcjami dostarczone w [sygnałów dynamicznych pojedynczego logowania jednokrotnego samouczek](dynamicsignal-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla dynamicznych sygnału w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Dynamic Signal**.

    ![Link aplikacji Dynamic Signal na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy** i **klucz tajny tokenu** konta sygnałów dynamicznych, zgodnie z opisem w kroku 6.

6. W konsoli administracyjnej sygnałów dynamicznych, przejdź do **Administrator > Zaawansowane > Interfejs API**.

    ![Sygnał dynamicznego inicjowania obsługi administracyjnej.](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Kopiuj **adresu URL interfejsu API Standard SCIM** do **dzierżawy adresu URL**. Kliknij pozycję **wygenerować nowy Token** do generowania **tokenu elementu nośnego** i skopiuj wartość do **klucz tajny tokenu**.

    ![Sygnał dynamicznego inicjowania obsługi administracyjnej.](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby upewnij się, Azure AD można połączyć się z sygnałów dynamicznych. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto sygnałów dynamicznych ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + tokenu](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do dynamicznego sygnału**.

    ![Mapowania użytkownika sygnałów dynamicznych](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do dynamicznego sygnału w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w dynamicznych sygnał dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkownika sygnałów dynamicznych](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD usługi sygnał dynamicznego inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać je na sygnał dynamiczne, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD usługi na sygnał dynamicznego inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Dynamiczne sygnału nie obsługuje usuwa trwały użytkownika z usługi Azure AD. Aby usunąć użytkownika trwale sygnałów dynamicznych, operacja musi zostać wykonana za pomocą konsoli administracyjnej sygnału dynamicznego interfejsu użytkownika. 
* Dynamiczne sygnału aktualnie nie obsługuje grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

