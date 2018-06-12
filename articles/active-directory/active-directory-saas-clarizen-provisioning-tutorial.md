---
title: 'Samouczek: Konfigurowanie Clarizen dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do Clarizen.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 5ee0d5248e3e53db8b7475dca1d51658ade25f81
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293266"
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Clarizen dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w Clarizen i Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD automatycznie zapewnianie i usuwanie użytkowników i/lub grup w celu Clarizen.

> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz następujące:

*   Dzierżawa usługi Azure AD
*   Dzierżawcy Clarizen z [Enterprise Edition](https://www.clarizen.com/product/pricing/) plan lub lepiej jest włączone 
*   Konto użytkownika z uprawnieniami administratora w Clarizen

> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [Clarizen API](https://api.clarizen.com/v2.0/services/), który jest dostępny dla zespołów Clarizen na plan Enterprise Edition lub większą.

## <a name="adding-clarizen-from-the-gallery"></a>Dodawanie Clarizen z galerii
Przed skonfigurowaniem Clarizen dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure AD, należy dodać Clarizen z galerii aplikacji Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Clarizen z galerii aplikacji Azure AD, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje przedsiębiorstwa sekcji][2]
    
3. Aby dodać Clarizen, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Clarizen**.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. W panelu wyników wybierz **Clarizen**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać Clarizen do listy aplikacji SaaS.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Przypisywanie użytkowników do Clarizen

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatycznego inicjowania obsługi administracyjnej są synchronizowane tylko użytkowników i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD. 

Przed Konfigurowanie i włączanie automatycznej aprowizacji użytkowników, należy podjąć decyzję dotyczącą użytkowników i/lub grup w usłudze Azure AD będą potrzebować dostępu do Clarizen. Po decyzję, można przypisać użytkowników i/lub grup Clarizen postępując zgodnie z instrukcjami poniżej:

*   [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Ważne porady dotyczące przypisywania użytkowników do Clarizen

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD Clarizen próba automatycznego przypisywania konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania Clarizen użytkownika, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. Użytkownicy z **domyślnego dostępu** roli są wykluczone z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Konfigurowanie użytkownika automatycznego inicjowania obsługi administracyjnej Clarizen 

W tej sekcji przedstawiono kroki, aby skonfigurować usługi Azure AD świadczenie usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup Clarizen — w oparciu o przypisania użytkownika i/lub grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć na języku SAML rejestracji jednokrotnej dla Clarizen, wykonując instrukcje podane w [Clarizen pojedynczego logowania jednokrotnego samouczek](active-directory-saas-clarizen-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od użytkownika automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Clarizen w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz Clarizen z listy aplikacji SaaS.
 
    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Wybierz **inicjowania obsługi administracyjnej** kartę.
    
    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **domeny**, **administratora**, i **hasło administratora** Twojego Clarizen konta. Przykłady te wartości są:

    *   W **administratora** pola, nazwa użytkownika konta administratora dzierżawy Clarizen wypełniania. Przykład: admin@contoso.com.

    *   W **hasło administratora** pola, wypełnij hasło konta administratora odpowiadające nazwa użytkownika.

    *   W **domeny** pola, wypełnij poddomeny na podstawie w kroku 6.
    
6. Pobrać **serverLocation** oparte na czynności wymienionych w obszarze konta Clarizen **uwierzytelniania** z [Podręcznik interfejsu API Rest na Clarizen](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2). Po uzyskaniu serverLocation, użyj poddomenę tego adresu URL, jak wyróżniono poniżej, aby wypełnić **domeny** pola.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. Przy wprowadzaniu pola pokazane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z Clarizen. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Clarizen ma uprawnienia administratora i spróbuj ponownie.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny odbierać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **wysłać wiadomość e-mail z powiadomieniem, gdy wystąpi błąd**.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom Clarizen**.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD Clarizen w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Clarizen dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do Clarizen**.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD Clarizen w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania grup w Clarizen dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Do konfigurowania filtrów zakresu, zapoznaj się z następujących instrukcji podanych w [Scoping filtru samouczek](./active-directory-saas-scoping-filters.md).

15. Aby włączyć usługi Azure AD usługi dla Clarizen inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać Clarizen, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. Gdy wszystko będzie gotowe, aby udostępnić, kliknij przycisk **zapisać**.

    ![Clarizen inicjowania obsługi administracyjnej.](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


Ta operacja uruchamia synchronizacji początkowej wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej działania raport, który opisuje wszystkie akcje wykonywane przez usługę Azure AD usługi na Clarizen inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
