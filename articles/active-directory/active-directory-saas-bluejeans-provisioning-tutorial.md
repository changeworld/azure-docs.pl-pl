---
title: 'Samouczek: Konfigurowanie BlueJeans dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.openlocfilehash: 9b071d166f741df523d6343ba771b7a29cd4a303
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie BlueJeans dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w BlueJeans i Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD automatycznie zapewnianie i usuwanie użytkowników i/lub grup w celu BlueJeans.

> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz następujące:

*   Dzierżawa usługi Azure AD
*   Dzierżawcy BlueJeans z [Moja firma](https://www.BlueJeans.com/pricing) plan lub lepiej jest włączone
*   Konto użytkownika z uprawnieniami administratora w BlueJeans

> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [BlueJeans API](https://BlueJeans.github.io/developer), który jest dostępny dla zespołów BlueJeans w planie Standard lub większą.

## <a name="adding-bluejeans-from-the-gallery"></a>Dodawanie BlueJeans z galerii
Przed skonfigurowaniem BlueJeans dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure AD, należy dodać BlueJeans z galerii aplikacji Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać BlueJeans z galerii aplikacji Azure AD, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje przedsiębiorstwa sekcji][2]
    
3. Aby dodać BlueJeans, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **BlueJeans**.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. W panelu wyników wybierz **BlueJeans**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać BlueJeans do listy aplikacji SaaS.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Przypisywanie użytkowników do BlueJeans

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatycznego inicjowania obsługi administracyjnej są synchronizowane tylko użytkowników i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD.

Przed Konfigurowanie i włączanie automatycznej aprowizacji użytkowników, należy podjąć decyzję dotyczącą użytkowników i/lub grup w usłudze Azure AD będą potrzebować dostępu do BlueJeans. Po decyzję, można przypisać użytkowników i/lub grup BlueJeans postępując zgodnie z instrukcjami poniżej:

*   [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Ważne porady dotyczące przypisywania użytkowników do BlueJeans

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD BlueJeans próba automatycznego przypisywania konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania BlueJeans użytkownika, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. Użytkownicy z **domyślnego dostępu** roli są wykluczone z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurowanie użytkownika automatycznego inicjowania obsługi administracyjnej BlueJeans

W tej sekcji przedstawiono kroki, aby skonfigurować usługi Azure AD świadczenie usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup BlueJeans — w oparciu o przypisania użytkownika i/lub grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć na języku SAML rejestracji jednokrotnej dla BlueJeans, wykonując instrukcje podane w [BlueJeans pojedynczego logowania jednokrotnego samouczek](active-directory-saas-bluejeans-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od użytkownika automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla BlueJeans w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz BlueJeans z listy aplikacji SaaS.
 
    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Wybierz **inicjowania obsługi administracyjnej** kartę.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans1.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **administratora**, i **hasło administratora** Twojego konta BlueJeans. Przykłady te wartości są:

    *   W **administratora** pola, nazwa użytkownika konta administratora dzierżawy BlueJeans wypełniania. Przykład: admin@contoso.com.

    *   W **hasło administratora** pola, wypełnij hasła odpowiadającego nazwa użytkownika.

6. Przy wprowadzaniu pola pokazane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z BlueJeans. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto BlueJeans ma uprawnienia administratora i spróbuj ponownie.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny odbierać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **wysłać wiadomość e-mail z powiadomieniem, gdy wystąpi błąd**.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Kliknij pozycję **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom BlueJeans**.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD BlueJeans w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w BlueJeans dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Do konfigurowania filtrów zakresu, zapoznaj się z następujących instrukcji podanych w [Scoping filtru samouczek](./active-directory-saas-scoping-filters.md).

12. Aby włączyć usługi Azure AD usługi dla BlueJeans inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać BlueJeans, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Gdy wszystko będzie gotowe, aby udostępnić, kliknij przycisk **zapisać**.

    ![BlueJeans Provisioning](./media/active-directory-saas-bluejeans-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia synchronizacji początkowej wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej działania raport, który opisuje wszystkie akcje wykonywane przez usługę Azure AD usługi na BlueJeans inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](./active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Bluejeans nie zezwala na nazwy użytkowników więcej niż 30 znaków.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-enterprise-apps-manage-provisioning.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
