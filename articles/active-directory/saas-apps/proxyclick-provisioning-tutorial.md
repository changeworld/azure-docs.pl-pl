---
title: 'Samouczek: Konfigurowanie Proxyclick dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Proxyclick.
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
ms.openlocfilehash: 2117f481d213c14f8feeb23eb8af3670db81cab4
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276641"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Proxyclick dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Proxyclick i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Proxyclick.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Proxyclick](https://www.proxyclick.com/pricing)
* Konto użytkownika w Proxyclick z uprawnieniami administratora.

## <a name="add-proxyclick-from-the-gallery"></a>Dodaj Proxyclick z galerii

Przed skonfigurowaniem Proxyclick dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać Proxyclick z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Proxyclick z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Proxyclick**, wybierz opcję **Proxyclick** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Proxyclick na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Przypisywanie użytkowników do Proxyclick

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Proxyclick. Po decyzję, możesz przypisać użytkownikom i/lub grup Proxyclick zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Proxyclick

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Proxyclick do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Proxyclick, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Proxyclick 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Proxyclick oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Proxyclick, wykonując instrukcje podane w [Proxyclick pojedynczego logowania jednokrotnego samouczek](proxyclick-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Proxyclick w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Proxyclick**.

    ![Link Proxyclick na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. Aby pobrać **adres URL dzierżawy** i **klucz tajny tokenu** konta Proxyclick, postępuj zgodnie z tym przewodnikiem zgodnie z opisem w kroku 6.

6. Zaloguj się do Twojej [konsoli administracyjnej Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Przejdź do **ustawienia** > **integracje** > **Przeglądaj Portal Marketplace**.

    ![Ustawienia Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integracje Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Wybierz **usługi Azure AD**. Kliknij przycisk **Zainstaluj teraz**.

    ![Proxyclick usługi Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Zainstaluj Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Wybierz **aprowizacji użytkowników** i kliknij przycisk **Start integracji**. 

    ![Aprowizacja użytkowników Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    Konfiguracja odpowiednie ustawienia interfejsu użytkownika powinny być teraz widoczne w obszarze **ustawienia** > **integracje**. Wybierz **ustawienia** w obszarze **usługi Azure AD (Inicjowanie obsługi użytkowników)** .

    ![Proxyclick Create](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Możesz znaleźć **adres URL dzierżawy** i **klucz tajny tokenu** tutaj.

    ![Proxyclick Utwórz Token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Proxyclick. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Proxyclick ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Proxyclick**.

    ![Mapowania użytkownika Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Proxyclick w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Proxyclick operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkownika Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługi Azure AD, usługi dla Proxyclick inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

15. Definiowanie użytkowników i/lub grup, które chcesz, aby obsługiwać je na Proxyclick, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

16. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Proxyclick aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Wymaga Proxyclick **wiadomości e-mail** i **userName** można mieć taką samą wartość źródła. Wszelkie aktualizacje do obu atrybutów zmodyfikuje inną wartość.
* Proxyclick nie obsługuje udostępniania dla grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

