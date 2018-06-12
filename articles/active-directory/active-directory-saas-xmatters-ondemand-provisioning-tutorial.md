---
title: 'Samouczek: Konfigurowanie xMatters na żądanie dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do xMatters OnDemand.
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
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 52fe23f5353a68e3afbac6a91955a61c7b8fb44c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292674"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie xMatters na żądanie dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w xMatters OnDemand i Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD, aby automatycznie zapewnianie i usuwanie użytkowników i/lub grup w celu xMatters na żądanie.

> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz następujące:

*   Dzierżawa usługi Azure AD
*   Dzierżawcy OnDemand xMatters z [Starter](https://www.xmatters.com/pricing) plan lub lepiej jest włączone 
*   Konto użytkownika w xMatters na żądanie z uprawnieniami administratora

> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [xMatters OnDemand API](https://help.xmatters.com/xmAPI), który jest dostępny dla zespołów OnDemand xMatters w planie Starter lub większą.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Dodawanie xMatters na żądanie z galerii

Przed skonfigurowaniem xMatters na żądanie dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure AD, należy dodać xMatters na żądanie z galerii aplikacji Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać xMatters na żądanie z galerii aplikacji Azure AD, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje przedsiębiorstwa sekcji][2]
    
3. Aby dodać xMatters na żądanie, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **xMatters OnDemand**.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. W panelu wyników wybierz **xMatters OnDemand**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać do listy aplikacji SaaS xMatters OnDemand.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Przypisywanie użytkowników do xMatters na żądanie

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatycznego inicjowania obsługi administracyjnej są synchronizowane tylko użytkowników i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD.

Przed Konfigurowanie i włączanie automatycznej aprowizacji użytkowników, należy podjąć decyzję dotyczącą użytkowników i/lub grup w usłudze Azure AD będą potrzebować dostępu do xMatters OnDemand. Po decyzję, można przypisać użytkowników i/lub grup xMatters OnDemand postępując zgodnie z instrukcjami poniżej:

*   [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Ważne porady dotyczące przypisywania użytkowników do xMatters na żądanie

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD xMatters OnDemand do testowania automatycznego przypisywania konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania xMatters na żądanie użytkownika, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. Użytkownicy z **domyślnego dostępu** roli są wykluczone z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Konfigurowanie użytkownika automatycznego inicjowania obsługi administracyjnej xMatters na żądanie 

W tej sekcji przedstawiono kroki, aby skonfigurować usługi Azure AD świadczenie usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grup w xMatters, które OnDemand na podstawie użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć na języku SAML rejestracji jednokrotnej dla xMatters OnDemand, wykonując instrukcje podane w [xMatters OnDemand rejestracji jednokrotnej samouczek](active-directory-saas-xmatters-ondemand-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od użytkownika automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla xMatters OnDemand w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz xMatters na żądanie z listy aplikacji SaaS.
 
    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Wybierz **inicjowania obsługi administracyjnej** kartę.
    
    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **administratora**, **hasło administratora**, i **domeny** z Twojej xMatters OnDemand w konto.

    *   W **administratora** pola, wypełnij nazwę użytkownika konta administratora dzierżawy xMatters na żądanie. Przykład: admin@contoso.com.

    *   W **hasło administratora** pola, wypełnij hasła odpowiadającego nazwa użytkownika.

    *   W **domeny** pola, wypełnić poddomeny dzierżawy xMatters na żądanie.
    Przykład: dla konta z adresem URL dzierżawy https://my-tenant.xmatters.com, użytkownika domeny podrzędnej byłaby **Moje dzierżawy**.

6. Przy wprowadzaniu pola pokazane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z xMatters OnDemand. Jeśli połączenie nie powiedzie się, upewnij się, Twoje xMatters OnDemand konto ma uprawnienia administratora i spróbuj ponownie.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny odbierać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **wysłać wiadomość e-mail z powiadomieniem, gdy wystąpi błąd**.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Kliknij pozycję **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom xMatters OnDemand**.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD xMatters OnDemand w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w xMatters OnDemand dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do xMatters OnDemand**.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD xMatters OnDemand w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania grup w xMatters OnDemand dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Do konfigurowania filtrów zakresu, zapoznaj się z następujących instrukcji podanych w [Scoping filtru samouczek](./active-directory-saas-scoping-filters.md).

14. Aby włączyć usługi Azure AD, świadczenie usługi dla xMatters OnDemand, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać xMatters na żądanie, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Gdy wszystko będzie gotowe, aby udostępnić, kliknij przycisk **zapisać**.

    ![xMatters udostępniania na żądanie](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia synchronizacji początkowej wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej działania raport, który opisuje wszystkie akcje wykonywane przez usługę Azure AD, świadczenie usługi na xMatters OnDemand.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
