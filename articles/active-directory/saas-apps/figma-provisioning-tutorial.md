---
title: 'Samouczek: Konfigurowanie Figma automatyczna aprowizacja użytkowników przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Figma.
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
ms.openlocfilehash: b71aa6709b1c93688ea3eece4ce3f4066f9a6b7a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673173"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Figma dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Figma i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Figma.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania dla funkcji w wersji zapoznawczej, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych platformy Microsoft](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawy Figma](https://www.figma.com/pricing/).
* Konto użytkownika w Figma z uprawnieniami administratora.

## <a name="assign-users-to-figma"></a>Przypisywanie użytkowników do Figma.
Usługa Azure Active Directory używa koncepcji o nazwie przypisania, aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Figma. Po decyzję, możesz przypisać użytkownikom i/lub grup Figma zgodnie z instrukcjami w tym miejscu:
 
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Figma

 * Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Figma do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Figma, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z rolą domyślnego dostępu są wykluczane z inicjowania obsługi administracyjnej.

## <a name="set-up-figma-for-provisioning"></a>Konfigurowanie Figma do inicjowania obsługi

Przed skonfigurowaniem Figma dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, należy pobrać niektóre informacje o udostępnianiu z Figma.

1. Zaloguj się do Twojej [konsoli administracyjnej Figma](https://www.Figma.com/). Kliknij ikonę koła zębatego obok Twojej dzierżawy.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/image0.png)

2. Przejdź do **ogólne > Aktualizacja dziennika w ustawieniach**.

    ![FigmaFigma-employee-provision](media/Figma-provisioning-tutorial/figma03.png)

3. Kopiuj **identyfikator dzierżawy**. Ta wartość będzie używana do utworzenia adresu URL punktu końcowego Standard SCIM wprowadzonych do **adres URL dzierżawy** pola na karcie Aprowizacja Figma aplikacji w witrynie Azure portal.

    ![Figma Utwórz Token](media/Figma-provisioning-tutorial/figma-tenantid.png)

4. Przewiń w dół i kliknij pozycję **Generuj Token interfejsu API**.

    ![Figma Utwórz Token](media/Figma-provisioning-tutorial/token.png)

5. Kopiuj **Token interfejsu API** wartość. Ta wartość zostanie wprowadzona w **klucz tajny tokenu** pola na karcie Aprowizacja Figma aplikacji w witrynie Azure portal. 

    ![Figma Utwórz Token](media/Figma-provisioning-tutorial/figma04.png)

## <a name="add-figma-from-the-gallery"></a>Dodaj Figma z galerii

Aby skonfigurować Figma dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, należy dodać Figma z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

1. W  **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okienka.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Figma**, wybierz opcję **Figma** panel wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Figma na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Figma 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Figma oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Figma, wykonując instrukcje podane w [Figma pojedynczego logowania jednokrotnego samouczek](figma-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Figma w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Figma**.

    ![Link Figma na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Udostępnianie](common/provisioning.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Udostępnianie](common/provisioning-automatic.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych `https://www.figma.com/scim/v2/<TenantID>` w **adres URL dzierżawy** gdzie **TenantID** jest wartością, która pobranym z Figma wcześniej. Dane wejściowe **Token interfejsu API** wartość w **klucz tajny tokenu**. Kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Figma. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Figma ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + tokenu](common/provisioning-testconnection-tenanturltoken.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij polecenie **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Figma**.

    ![Mapowania użytkownika Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Figma w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Figma operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Atrybuty użytkownika Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD, usługi dla Figma inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Stan aprowizacji przełączona w](common/provisioning-toggle-on.png)

14. Definiowanie użytkowników i/lub grup, które chcesz, aby obsługiwać je na Figma, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej zakresu](common/provisioning-scope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Figma aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)