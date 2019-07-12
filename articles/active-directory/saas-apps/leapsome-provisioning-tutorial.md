---
title: 'Samouczek: Konfigurowanie Leapsome dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Leapsome.
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
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672691"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Leapsome dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Leapsome i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Leapsome.

> [!NOTE]
>  W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* A [Leapsome](https://www.Leapsome.com/en/pricing) dzierżawy.
* Konto użytkownika w Leapsome z uprawnieniami administratora.

## <a name="assigning-users-to-leapsome"></a>Przypisywanie użytkowników do Leapsome

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Leapsome. Po decyzję, możesz przypisać użytkownikom i/lub grup Leapsome zgodnie z instrukcjami w tym miejscu:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Leapsome

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Leapsome do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Leapsome, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.


## <a name="setup-leapsome-for-provisioning"></a>Konfigurowanie Leapsome do inicjowania obsługi

1. Zaloguj się do Twojej [konsoli administracyjnej Leapsome](https://www.Leapsome.com/app/#/login). Przejdź do **Ustawienia > Ustawienia administratora**.

    ![Konsola administracyjna Leapsome](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Przejdź do **integracji > aprowizacji użytkowników Standard SCIM**.

    ![Leapsome Dodaj Standard SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Kopiuj **Token uwierzytelniania Standard SCIM**. Ta wartość zostanie wprowadzona w taki sposób, w polu klucz tajny tokenu na karcie Aprowizacja Leapsome aplikacji w witrynie Azure portal.

    ![Leapsome Utwórz Token](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Dodaj Leapsome z galerii

Przed skonfigurowaniem Leapsome dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać Leapsome z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Leapsome z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Leapsome**, wybierz opcję **Leapsome** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Leapsome na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Leapsome 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Leapsome oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Leapsome, wykonując instrukcje podane w [Leapsome pojedynczego logowania jednokrotnego samouczek](Leapsome-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Leapsome w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Leapsome**.

    ![Link Leapsome na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych `https://www.leapsome.com/api/scim` w **adres URL dzierżawy**. Dane wejściowe **tokenu uwierzytelniania Standard SCIM** wartość pobranego wcześniej w **klucz tajny tokenu**. Kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Leapsome. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Leapsome ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + tokenu](common/provisioning-testconnection-tenanturltoken.png)

6. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij polecenie **Zapisz**.

8. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Leapsome**.

    ![Mapowania użytkownika Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Leapsome w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Leapsome operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkownika Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do Leapsome**.

    ![Mapowania Leapsome grupy](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD Leapsome w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w Leapsome dla operacji aktualizowania. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty grupy Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD, usługi dla Leapsome inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

14. Definiowanie użytkowników i/lub grup, które chcesz, aby obsługiwać je na Leapsome, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Leapsome aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Wymaga Leapsome **userName** być unikatowe.
* Leapsome zezwala tylko służbowe adresy e-mail do zapisania.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)
