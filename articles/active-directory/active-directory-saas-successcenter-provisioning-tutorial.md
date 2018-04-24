---
title: "Samouczek: Konfigurowanie podstawy OnDemand dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do podstawy OnDemand."
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8aed9bdcdd54f2f0ef3cd81f979635e5716f0b5f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie podstawy OnDemand dla użytkownika automatycznego inicjowania obsługi administracyjnej.


Celem tego samouczka jest aby zademonstrować czynności wykonywanych w OnDemand podstawy i Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD automatycznie zapewnianie i usuwanie użytkowników i/lub grup w celu OnDemand podstawy.


> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz następujące:

*   Dzierżawa usługi Azure AD
*   Dzierżawy OnDemand podstawy
*   Konto użytkownika w OnDemand podstawy z uprawnieniami administratora


> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [podstawy na żądanie usługi sieci Web](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), które są dostępne dla zespołów OnDemand podstawy.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Dodawanie atrybutu podstawy z galerii
Przed skonfigurowaniem OnDemand podstawy dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure AD, należy dodać OnDemand podstawy z galerii aplikacji Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać OnDemand podstawy z galerii aplikacji Azure AD, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje przedsiębiorstwa sekcji][2]
    
3. Aby dodać podstawy na żądanie, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **OnDemand podstawy**.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearch.png)

5. W panelu wyników wybierz **OnDemand podstawy**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać do listy aplikacji SaaS OnDemand podstawy.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearchResults.png)

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Przypisywanie użytkowników do OnDemand podstawy

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatycznego inicjowania obsługi administracyjnej są synchronizowane tylko użytkowników i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD. 

Przed Konfigurowanie i włączanie automatycznej aprowizacji użytkowników, należy podjąć decyzję dotyczącą użytkowników i/lub grup w usłudze Azure AD będą potrzebować dostępu do podstawy OnDemand. Po decyzję, można przypisać użytkowników i/lub grup OnDemand podstawy postępując zgodnie z instrukcjami poniżej:

*   [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Ważne porady dotyczące przypisywania użytkowników do OnDemand podstawy

*   Zalecane jest pojedynczego użytkownika usługi Azure AD jest przypisana do OnDemand podstawy, aby przetestować użytkownika automatyczne Inicjowanie obsługi konfiguracji. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania podstawy na żądanie użytkownika, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. Użytkownicy z **domyślnego dostępu** roli są wykluczone z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurowanie użytkownika automatycznego inicjowania obsługi administracyjnej OnDemand podstawy

W tej sekcji przedstawiono kroki, aby skonfigurować usługi Azure AD świadczenie usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grup w OnDemand podstawy, w oparciu o użytkownika i/lub przypisania grupy w usłudze Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla podstawy OnDemand w usłudze Azure AD:


1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz OnDemand podstawy z listy aplikacji SaaS.
 
    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/Successcenter2.png)

3. Wybierz **inicjowania obsługi administracyjnej** kartę.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **administratora**, **hasło administratora**, i **domeny** z Twojej OnDemand podstawy konto.

    *   W **administratora** pola, wypełnij nazwę użytkownika konta administratora dzierżawy OnDemand podstawy. Przykład: administratora.

    *   W **hasło administratora** pola, wypełnij hasła odpowiadającego nazwa użytkownika.

    *   W **domeny** pola, wypełnij podstawy adres URL usługi sieci Web. Przykład: Usługa znajduje się w `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`domeny Contoso jest `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`.

6. Przy wprowadzaniu pola pokazane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z OnDemand podstawy. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto OnDemand podstawy ma uprawnienia administratora i spróbuj ponownie.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/TestConnection.png)

7. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny odbierać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **wysłać wiadomość e-mail z powiadomieniem, gdy wystąpi błąd**.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/EmailNotification.png)

8. Kliknij pozycję **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do podstawy OnDemand**.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMapping.png)

10. Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD do podstawy OnDemand w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w OnDemand podstawy dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMappingAttributes.png)

11. Do konfigurowania filtrów zakresu, zapoznaj się z następujących instrukcji podanych w [Scoping filtru samouczek](./active-directory-saas-scoping-filters.md).

12. Aby włączyć usługi Azure AD usługi dla podstawy OnDemand inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningStatus.png)

13. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać OnDemand podstawy, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/SyncScope.png)

14. Gdy wszystko będzie gotowe, aby udostępnić, kliknij przycisk **zapisać**.

    ![Podstawy OnDemand inicjowania obsługi administracyjnej.](./media/active-directory-saas-successcenter-provisioning-tutorial/Save.png) 


Ta operacja uruchamia synchronizacji początkowej wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej działania raport, który opisuje wszystkie akcje wykonywane przez usługę Azure AD, świadczenie usługi na OnDemand podstawy.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](./active-directory-saas-provisioning-reporting.md).
## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-enterprise-apps-manage-provisioning.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_03.png
