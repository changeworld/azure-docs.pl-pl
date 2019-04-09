---
title: 'Samouczek: Konfigurowanie Tableau Online dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do usługi Online firmy Tableau.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f732eebd410a6b52a21a46925a29bf4676f7c8cb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270790"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Tableau Online dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokaz czynności wykonywanych w Tableau Online i usługi Azure Active Directory (Azure AD) do konfigurowania usługi Azure AD do automatycznego aprowizowania lub cofania aprowizacji użytkowników i/lub grup do Tableau w trybie Online.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz już następujące czynności:

*   Dzierżawa usługi Azure AD
*   A [dzierżawy Tableau Online](https://www.tableau.com/)
*   Konto użytkownika w usłudze Tableau Online z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API Rest usługi Online firmy Tableau](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm), który jest dostępny dla deweloperów firmy Tableau w trybie Online.

## <a name="adding-tableau-online-from-the-gallery"></a>Dodawanie Tableau Online za pomocą galerii
Przed skonfigurowaniem Tableau Online dla automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać Tableau Online z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Tableau Online z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Tableau Online**, wybierz opcję **Tableau Online** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TABLEAU Online na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-tableau-online"></a>Przypisywanie użytkowników do usługi Online firmy Tableau

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do usługi Online firmy Tableau. Po decyzję, należy przypisać użytkowników i/lub grup do usługi Online firmy Tableau, zgodnie z instrukcjami w tym miejscu:

*   [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Online firmy Tableau

*   Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Tableau Online do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkownika Tableau online, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Konfigurowanie automatycznej aprowizacji użytkowników Tableau online

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączyć użytkowników i/lub grup w Tableau w trybie Online oparte na użytkownika i/lub przypisania grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowanie jednokrotne dla usługi Online firmy Tableau, postępując zgodnie z instrukcjami dostarczone w [Tableau pojedynczego logowania jednokrotnego samouczek Online](tableauonline-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Tableau Online w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Tableau Online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Tableau Online**.

    ![Tableau Online łącze na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **domeny**, **nazwa użytkownika administratora**, **hasło administratora**, i **zawartości Adres URL** konta Online firmy Tableau:

   * W **domeny** pola, wypełnij poddomeny, w oparciu o krok 6.

   * W **nazwa użytkownika administratora** pola, wypełnij nazwę użytkownika konta administratora w Twojej dzierżawie Clarizen. Przykład: admin@contoso.com.

   * W **hasło administratora** pola, wypełnij hasło konta administratora, odpowiadający nazwa użytkownika administratora.

   * W **adres URL zawartości** pola, wypełnij poddomeny, w oparciu o krok 6.

6. Po zalogowaniu się do swojego konta administracyjne dla usługi Online firmy Tableau wartości **domeny** i **adres URL zawartości** można wyodrębnić z adres URL strony administratora.

    * **Domeny** dla usługi Online firmy Tableau można skopiować konta z tej części adresu URL:

        ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **Adres URL zawartości** dla usługi Online firmy Tableau można skopiować konta z tej sekcji i zdefiniowano wartości podczas konfigurowania konta. W tym przykładzie wartość to "contoso":

        ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Twoje **domeny** może różnić się od przedstawionego w tym miejscu.

7. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** zapewniające usługi Azure AD connect można Tableau online. Jeśli połączenie nie powiedzie się, upewnij się, że konta Online firmy Tableau ma uprawnienia administratora i spróbuj ponownie.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Kliknij pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Tableau**.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Tableau w trybie Online w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w dokumentacji Online Tableau operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do Tableau**.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD Tableau w trybie Online w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w dokumentacji Online Tableau operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD, inicjowanie obsługi administracyjnej dla usługi Online firmy Tableau, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Zdefiniować użytkowników i/lub grup, które chcesz aprowizować Tableau online, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do inicjowania obsługi administracyjnej raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, usługi online firmy Tableau inicjowania obsługi administracyjnej.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
