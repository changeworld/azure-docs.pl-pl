---
title: 'Samouczek: Konfigurowanie Replicon dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do Replicon.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 0f71c449e2fb0ab8b26ee9ba68850cbf7fe03b28
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214750"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Replicon dla użytkownika automatycznego inicjowania obsługi administracyjnej.

Celem tego samouczka jest aby zademonstrować czynności wykonywanych w Replicon i Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD automatycznie zapewnianie i usuwanie użytkowników i/lub grup w celu Replicon.

> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące elementy:

*   Dzierżawa usługi Azure AD
*   Dzierżawcy Replicon z [Plus](https://www.replicon.com/time-bill-pricing/) plan lub lepiej jest włączone
*   Konto użytkownika z uprawnieniami administratora w Replicon

> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [Replicon API](https://www.replicon.com/help/developers), które są dostępne dla zespołów Replicon na Plus plan lub większą.

## <a name="adding-replicon-from-the-gallery"></a>Dodawanie Replicon z galerii
Przed skonfigurowaniem Replicon dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure AD, należy dodać Replicon z galerii aplikacji Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Replicon z galerii aplikacji Azure AD, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje przedsiębiorstwa sekcji][2]
    
3. Aby dodać Replicon, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Replicon**.

    ![Replicon aplikacji wyszukiwania](./media/replicon-provisioning-tutorial/RepliconAppSearch.png)

5. W panelu wyników wybierz **Replicon**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać Replicon do listy aplikacji SaaS.

    ![Wyniki wyszukiwania replicon](./media/replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Replicon tworzenie aplikacji](./media/replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Przypisywanie użytkowników do Replicon

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatycznego inicjowania obsługi administracyjnej są synchronizowane tylko użytkowników i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD.

Przed Konfigurowanie i włączanie automatycznej aprowizacji użytkowników, należy podjąć decyzję dotyczącą użytkowników i/lub grup w usłudze Azure AD będą potrzebować dostępu do Replicon. Po decyzję, można przypisać użytkowników i/lub grup Replicon postępując zgodnie z instrukcjami poniżej:

*   [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Ważne porady dotyczące przypisywania użytkowników do Replicon

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD Replicon próba automatycznego przypisywania konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania Replicon użytkownika, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. Użytkownicy z **domyślnego dostępu** roli są wykluczone z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Konfigurowanie użytkownika automatycznego inicjowania obsługi administracyjnej Replicon 

W tej sekcji przedstawiono kroki, aby skonfigurować usługi Azure AD świadczenie usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup Replicon — w oparciu o przypisania użytkownika i/lub grup w usłudze Azure AD.

> [!TIP]
> Można też włączyć na języku SAML rejestracji jednokrotnej dla Replicon, wykonując instrukcje podane w [Replicon pojedynczego logowania jednokrotnego samouczek](replicon-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od użytkownika automatycznego inicjowania obsługi administracyjnej, że te dwie funkcje uzupełniania siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Replicon w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz Replicon z listy aplikacji SaaS.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/Replicon2.png)

3. Wybierz **inicjowania obsługi administracyjnej** kartę.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/Replicon1.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **administratora**, **hasło administratora**, **CompanyId**, i **domeny**  Twojego Replicon konta. Przykłady te wartości są:

    *   W **administratora** pola, nazwa użytkownika konta administratora dzierżawy Replicon wypełniania. Przykład: administrator_contoso.

    *   W **hasło administratora** pola, wypełnij hasła odpowiadającego nazwa użytkownika.

    *   W **CompanyId** pola, wypełnij CompanyId dzierżawy Replicon. Przykład: CompanyID oparte na dziennika w poniżej jest Contoso.

    ![Replicon logowania](./media/replicon-provisioning-tutorial/RepliconLogin.png)

    *   W **domeny** pola, wypełnij domeny, zgodnie z opisem w kroku 6.
    
6. Uzyskaj **serviceEndpointRootURL** dla Twojego Replicon dzierżawy konta czynności wymienionych w oparciu o [Pomoc usługi Replicon](https://www.replicon.com/help/determining-the-url-for-your-service-calls). Podczas uzyskiwania adresu URL, **domeny** poddomeną **serviceEndpointRootURL** jako zaznaczony. 

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/RepliconEndpoint.png)

7. Przy wprowadzaniu pola pokazane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z Replicon. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Replicon ma uprawnienia administratora i spróbuj ponownie.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/RepliconTestConnection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny odbierać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **wysłać wiadomość e-mail z powiadomieniem, gdy wystąpi błąd**.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom Replicon**.
    
    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD Replicon w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Replicon dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Do konfigurowania filtrów zakresu, zapoznaj się z następujących instrukcji podanych w [Scoping filtru samouczek](../active-directory-saas-scoping-filters.md).

13. Aby włączyć usługi Azure AD usługi dla Replicon inicjowania obsługi administracyjnej, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać Replicon, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/UserGroupSelection.png)

15. Gdy wszystko będzie gotowe, aby udostępnić, kliknij przycisk **zapisać**.

    ![Replicon inicjowania obsługi administracyjnej.](./media/replicon-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia synchronizacji początkowej wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej działania raport, który opisuje wszystkie akcje wykonywane przez usługę Azure AD usługi na Replicon inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/replicon-tutorial/tutorial_general_01.png
[2]: ./media/replicon-tutorial/tutorial_general_02.png
[3]: ./media/replicon-tutorial/tutorial_general_03.png
