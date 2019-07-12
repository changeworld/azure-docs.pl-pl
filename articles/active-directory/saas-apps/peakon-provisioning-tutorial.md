---
title: 'Samouczek: Konfigurowanie Peakon automatyczna aprowizacja użytkowników przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Peakon.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 2547f34432ca1b4b52f34c343bb4aad2f2407f53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673147"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Peakon dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Peakon i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Peakon.

> [!NOTE]
>  W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne

* Dzierżawa usługi Azure AD.
* [Dzierżawy Peakon](https://peakon.com/us/pricing/).
* Konto użytkownika w Peakon z uprawnieniami administratora.

## <a name="assigning-users-to-peakon"></a>Przypisywanie użytkowników do Peakon

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Peakon. Po decyzję, możesz przypisać użytkownikom i/lub grup Peakon zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Peakon 

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Peakon do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Peakon, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="set-up-peakon-for-provisioning"></a>Konfigurowanie Peakon do inicjowania obsługi

1.  Zaloguj się do Twojej [konsoli administracyjnej Peakon](https://app.Peakon.com/login). Kliknij pozycję **konfiguracji**. 

    ![Konsola administracyjna Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Wybierz **integracje**.
    
    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Włącz **pracowników aprowizacji**.

    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Skopiuj wartości pól **adresu URL w wersji 2.0 Standard SCIM** i **tokenu elementu nośnego OAuth**. Te wartości zostaną wprowadzone w **adres URL dzierżawy** i **klucz tajny tokenu** pola na karcie Aprowizacja Peakon aplikacji w witrynie Azure portal.

    ![Peakon Utwórz Token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Dodaj Peakon z galerii

W przypadku konfigurowania Peakon dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać Peakon z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Peakon**, wybierz opcję **Peakon** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Peakon na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Peakon 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Peakon oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Peakon, wykonując instrukcje podane w [Peakon pojedynczego logowania jednokrotnego samouczek](peakon-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Peakon w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Peakon**.

    ![Link Peakon na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adresu URL w wersji 2.0 Standard SCIM** i **tokenu elementu nośnego OAuth** wartości pobranego wcześniej w **adres URL dzierżawy** i **klucz tajny tokenu** odpowiednio. Kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Peakon. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Peakon ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + tokenu](common/provisioning-testconnection-tenanturltoken.png)

7. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

8. Kliknij polecenie **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Peakon**.

    ![Mapowania użytkownika Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Peakon w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Peakon operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkownika Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Peakon aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Wszystkie atrybuty użytkownika niestandardowego w Peakon musi zostać rozszerzony z firmy Peakon niestandardowe Standard SCIM użytkownika rozszerzenia `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)