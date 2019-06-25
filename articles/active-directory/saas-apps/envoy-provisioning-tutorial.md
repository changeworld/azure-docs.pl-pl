---
title: 'Samouczek: Konfigurowanie usługa Envoy dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników, aby usługa Envoy.
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
ms.openlocfilehash: a68a8ec7c5df678918add4fcc519cc1dd918cb86
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168079"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługa Envoy dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w usługa Envoy i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD, aby automatycznie aprowizować i anulować obsługę użytkowników i/lub grupy, aby usługa Envoy.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Usługa Envoy dzierżawy](https://envoy.com/pricing/)
* Konto użytkownika w usługa Envoy z uprawnieniami administratora.

## <a name="add-envoy-from-the-gallery"></a>Dodaj usługa Envoy z galerii

Przed skonfigurowaniem usługa Envoy dla automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać usługa Envoy z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać usługa Envoy z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **usługa Envoy**, wybierz opcję **usługa Envoy** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja Envoy na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Przypisywanie użytkowników do usługa Envoy

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do usługa Envoy. Po decyzję, możesz przypisać użytkownikom i/lub grup usługa Envoy zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługa Envoy

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do usługa Envoy do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do usługa Envoy, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Konfigurowanie automatycznej aprowizacji użytkowników do usługa Envoy 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w usługa Envoy oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla usługa Envoy, wykonując instrukcje podane w [usługa Envoy pojedynczego logowania jednokrotnego samouczek](envoy-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla usługa Envoy w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Envoy**.

    ![Link do aplikacji Envoy na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych `https://app.envoy.com/scim/v2` w **adres URL dzierżawy**. Aby pobrać **klucz tajny tokenu** konta Usługa Envoy, postępuj zgodnie z tym przewodnikiem zgodnie z opisem w kroku 6.

6. Zaloguj się do Twojej [konsoli administracyjnej usługa Envoy](https://dashboard.envoy.com/login). Kliknij pozycję **integracje**.

    ![Usługa envoy integracji](media/envoy-provisioning-tutorial/envoy01.png)

    Kliknij pozycję **zainstalować** dla **integracji programu Microsoft Azure w Standard SCIM**.

    ![Usługa envoy instalacji](media/envoy-provisioning-tutorial/envoy02.png)

    Kliknij pozycję **Zapisz** dla **synchronizowanie użytkowników z wszystkich**. 

    ![Usługa envoy Zapisz](media/envoy-provisioning-tutorial/envoy03.png)

    Pobierz wypełnione tokenu klucza tajnego.
    
    ![Usługa envoy OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z usługa Envoy. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Usługa Envoy ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do usługa Envoy**.
    
    ![Usługa envoy atrybutów użytkownika](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, aby usługa Envoy w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usługa Envoy operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Usługa envoy atrybutów użytkownika](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do usługa Envoy**.

    ![Usługa envoy mapowania użytkownika](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, aby usługa Envoy w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w usługa Envoy dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Usługa envoy mapowania użytkownika](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD, usługi dla usługa Envoy inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

16. Zdefiniować użytkowników i/lub grup, które chcesz, aby obsługiwać je na usługa Envoy, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na usługa Envoy.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

