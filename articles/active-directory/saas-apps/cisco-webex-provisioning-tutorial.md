---
title: 'Samouczek: Konfigurowanie Cisco dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Cisco Webex.
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
ms.openlocfilehash: 8f5af3cba01e925591c9d90ea0e96ed78b2823e2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348382"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Cisco Webex dla automatycznej aprowizacji użytkowników


Celem tego samouczka jest pokazują kroki do wykonania w Cisco Webex i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznego aprowizowania lub cofania aprowizacji użytkowników Webex firmy Cisco.


> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, iż już następujące wymagania wstępne:

*   Dzierżawa usługi Azure AD
*   Dzierżawy Cisco Webex
*   Konto użytkownika Cisco Webex z uprawnieniami administratora


> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [Cisco Webex Webservice](https://developer.webex.com/getting-started.html), który jest dostępny dla zespołów Webex firmy Cisco.

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie Cisco Webex z galerii
Przed skonfigurowaniem Cisco Webex dla automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać Cisco Webex z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Cisco Webex z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje w przedsiębiorstwie sekcji][2]

3. Aby dodać Cisco Webex, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Cisco Webex**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. W panelu wyników wybierz **Cisco Webex**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać Cisco Webex do listy aplikacji SaaS.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Przypisywanie użytkowników do Cisco Webex

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed konfigurowania i włączenie automatycznej aprowizacji użytkowników, należy zdecydować, użytkowników, którzy w usłudze Azure AD muszą mieć dostęp do Webex firmy Cisco. Po decyzję, można przypisać tych użytkowników do Cisco Webex, wykonując instrukcje podane w tym miejscu:

*   [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Cisco Webex

*   Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Cisco Webex do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników.

*   Podczas przypisywania użytkowników do Cisco Webex, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Cisco Webex

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników w Webex Cisco, w oparciu o przypisania użytkownika w usłudze Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Cisco Webex w usłudze Azure AD:


1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz Cisco Webex z listy aplikacji SaaS.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Wybierz **aprowizacji** kartę.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **adres URL dzierżawy**, i **klucz tajny tokenu** konta Webex Twojej firmy Cisco.

    *   W **adres URL dzierżawy** pola, wypełnij adresu URL interfejsu API Cisco Webex Standard SCIM dla dzierżawy, który ma postać `https://api.webex.com/v1/scim/[Tenant ID]/`, gdzie `[Tenant ID]` jest ciągiem alfanumerycznym o długości, zgodnie z opisem w kroku 6.

    *   W **klucz tajny tokenu** pola, wypełnij klucz tajny tokenu, zgodnie z opisem w kroku 6.

1. **Identyfikator dzierżawy** i **klucz tajny tokenu** dla Twojej firmy Cisco Webex konta można znaleźć, logując się do [witryny dla deweloperów firmy Cisco Webex](https://developer.webex.com/) przy użyciu konta administratora. Po zalogowaniu -
    * Przejdź do [Getting Started page](https://developer.webex.com/getting-started.html)
    * Przewiń w dół do [sekcji uwierzytelnianie](https://developer.webex.com/getting-started.html#authentication)
    ![tokenu uwierzytelniania Webex Cisco](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * Ciąg alfanumeryczny w polu jest Twoja **klucz tajny tokenu**. Skopiuj ten token do Schowka
    * Przejdź do [Pobierz moje własne szczegóły strony](https://developer.webex.com/endpoint-people-me-get.html)
        * Upewnij się, że tryb testowy jest ON
        * Wpisz słowo "Bearer" spację, a następnie wklej klucz tajny tokenu w polu autoryzacji ![tokenu uwierzytelniania Webex Cisco](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * Kliknij przycisk Uruchom
    * Tekst odpowiedzi z prawej strony **identyfikator dzierżawy** jest wyświetlany jako "z identyfikatorem organizacji":

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

1. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Cisco Webex. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Cisco Webex ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru - **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Cisco Webex**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Cisco Webex w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Cisco Webex dla operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługi Azure AD, usługi dla Cisco Webex inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Zdefiniować użytkowników i/lub grup, które chcesz, aby obsługiwać je na Cisco Webex, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Inicjowanie obsługi administracyjnej Webex Cisco](./media/cisco-webex-provisioning-tutorial/Save.png)


Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Cisco Webex.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Cisco Webex znajduje się obecnie w fazie wczesnego testowania pola (EFT) firmy Cisco. Aby uzyskać więcej informacji, skontaktuj się z pomocą [zespołem pomocy technicznej firmy Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png
