---
title: 'Samouczek: Konfigurowanie Cisco dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie zapewnianie i usuwanie kont użytkowników do Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: fdaf77e3d8a1858372298fb0d67ca05c2717adf6
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321150"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Cisco Webex dla użytkownika automatycznego inicjowania obsługi administracyjnej.


Celem tego samouczka jest aby zademonstrować czynności wykonywanych w Cisco Webex i Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD automatycznie zapewnianie i usuwanie użytkowników Cisco Webex.


> [!NOTE]
> W tym samouczku opisano łącznika, rozszerzający usługi dostarczania użytkownika programu Azure AD. Uzyskać ważne szczegóły dotyczące tej usługi nie, jak to działa i często zadawane pytania, zobacz [zautomatyzować użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że już następujące wymagania wstępne:

*   Dzierżawa usługi Azure AD
*   Dzierżawy Cisco Webex
*   Konto użytkownika z uprawnieniami administratora w Cisco Webex


> [!NOTE]
> Zależy od usługi Azure AD, inicjowania obsługi administracyjnej integracji [Cisco Webex Webservice](https://developer.webex.com/getting-started.html), które są dostępne dla zespołów Cisco Webex.

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie Cisco Webex z galerii
Przed skonfigurowaniem Cisco Webex dla użytkownika automatycznego inicjowania obsługi administracyjnej z usługą Azure AD, należy dodać Cisco Webex z galerii aplikacji Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Cisco Webex z galerii aplikacji Azure AD, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje przedsiębiorstwa sekcji][2]

3. Aby dodać Cisco Webex, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Cisco Webex**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. W panelu wyników wybierz **Cisco Webex**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać do listy aplikacji SaaS Cisco Webex.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Przypisywanie użytkowników do Cisco Webex

Usługi Azure Active Directory używa pojęcie o nazwie "przypisania" w celu określenia, którzy użytkownicy powinien otrzymać dostęp do wybranej aplikacji. W kontekście użytkownika automatycznego inicjowania obsługi administracyjnej są synchronizowane tylko użytkowników i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD.

Przed konfigurowania i włączenie użytkownika automatycznego inicjowania obsługi administracyjnej, należy podjąć decyzję dotyczącą użytkowników w usłudze Azure AD, którzy potrzebują dostępu do Cisco Webex. Po decyzję, można przypisać tych użytkowników do Cisco Webex, postępując zgodnie z instrukcjami poniżej:

*   [Przypisanie użytkownika lub grupę do aplikacji w przedsiębiorstwie](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Ważne porady dotyczące przypisywania użytkowników do Cisco Webex

*   Zalecane jest jeden jest przypisany użytkownik usługi Azure AD Cisco Webex próba automatycznego przypisywania konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników.

*   Podczas przypisywania Cisco Webex użytkownika, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przypisania. Użytkownicy z **domyślnego dostępu** roli są wykluczone z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurowanie użytkownika automatycznego inicjowania obsługi administracyjnej Cisco Webex

W tej sekcji przedstawiono kroki, aby skonfigurować usługi Azure AD świadczenie usługi do tworzenia, aktualizacji i wyłączanie użytkowników w oparciu o przypisania użytkownika w usłudze Azure AD Webex Cisco.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Cisco Webex w usłudze Azure AD:


1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz Cisco Webex z listy aplikacji SaaS.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Wybierz **inicjowania obsługi administracyjnej** kartę.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji wejściowych **adres URL dzierżawy**, i **klucz tajny tokenu** konta Webex Twojej firmy Cisco.

    *   W **adres URL dzierżawy** pola, wypełnij adres URL Cisco Webex SCIM interfejsu API dla swojej dzierżawy, która ma postać `https://api.webex.com/v1/scim/[Tenant ID]/`, gdzie `[Tenant ID]` jest alfanumerycznych zgodnie z opisem w kroku 6.

    *   W **klucz tajny tokenu** pola, wypełnij klucz tajny tokenu, zgodnie z opisem w kroku 6.

1. **Identyfikator dzierżawcy** i **klucz tajny tokenu** Twojego Webex Cisco można znaleźć konta logując się do [deweloperskiej Cisco Webex](https://developer.webex.com/) przy użyciu konta administratora. Po zalogowaniu -
    * Przejdź do [strony wprowadzenie](https://developer.webex.com/getting-started.html)
    * Przewiń w dół do [sekcja Authentication](https://developer.webex.com/getting-started.html#authentication)
    ![tokenu uwierzytelniania Webex Cisco](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * Alfanumeryczne ciąg w polu z **klucz tajny tokenu**. Skopiuj ten token do Schowka
    * Przejdź do [Pobierz moje własne szczegóły strony](https://developer.webex.com/endpoint-people-me-get.html)
        * Upewnij się, że tryb testowy jest ON
        * Wpisz wyraz "Bearer" spację, a następnie wklej klucz tajny tokenu do pola autoryzacji ![tokenu uwierzytelniania Webex Cisco](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * Kliknij przycisk Uruchom
    * Tekst odpowiedzi z prawej strony **identyfikator dzierżawcy** pojawia się jako "orgId":

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. Przy wprowadzaniu pola pokazane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD mogą łączyć się z Cisco Webex. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Cisco Webex ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny odbierać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **wysłać wiadomość e-mail z powiadomieniem, gdy wystąpi błąd**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure Active Directory użytkownikom Cisco Webex**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Przejrzyj atrybuty użytkowników, które są synchronizowane z usługi Azure AD Webex Cisco w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Cisco Webex dla operacji update. Wybierz **zapisać** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Do konfigurowania filtrów zakresu, zapoznaj się z następujących instrukcji podanych w [Scoping filtru samouczek](../active-directory-saas-scoping-filters.md).

13. Aby włączyć usługi Azure AD, świadczenie usługi dla Cisco Webex, zmień **stan inicjowania obsługi administracyjnej** do **na** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Zdefiniuj użytkowników i/lub grup, które chcesz aby obsługiwać Cisco Webex, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Gdy wszystko będzie gotowe, aby udostępnić, kliknij przycisk **zapisać**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/Save.png)


Ta operacja uruchamia synchronizacji początkowej wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 40 minut tak długo, jak działa usługi Azure AD, inicjowania obsługi usługi. Można użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej działania raport, który opisuje wszystkie akcje wykonywane przez usługę Azure AD usługi na Cisco Webex inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak należy przejrzeć dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png