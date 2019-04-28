---
title: 'Samouczek: Konfigurowanie Tableau Online dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub anulować aprowizację kont użytkowników do usługi Online firmy Tableau.
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
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123758"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Tableau Online dla automatycznej aprowizacji użytkowników

Ten samouczek przedstawia kroki do wykonania w Tableau Online i usługi Azure Active Directory (Azure AD) do konfigurowania usługi Azure AD do automatycznego aprowizowania lub anulować aprowizację użytkowników i grup do Tableau w trybie Online.

> [!NOTE]
> W tym samouczku opisano łącznika, który bazuje na usłudze usługa aprowizowania użytkowników w usłudze Azure AD. Aby uzyskać informacji na temat tej usługi nie, jak działa i często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i anulowania obsługi aplikacjom software-as-a-service (SaaS) w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz:

*   Dzierżawa usługi Azure AD.
*   A [dzierżawy Tableau Online](https://www.tableau.com/).
*   Konto użytkownika w usłudze Tableau Online z uprawnieniami administratora.

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API Rest usługi Online firmy Tableau](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Ten interfejs API jest dostępna dla deweloperów, Tableau w trybie Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Dodaj Tableau w trybie Online w portalu Azure Marketplace
Przed skonfigurowaniem Tableau Online dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD należy dodać Tableau Online w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS.

Aby dodać Tableau Online w portalu Marketplace, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Tableau Online** i wybierz **Tableau Online** z panelu wyników. Aby dodać aplikację, wybierz **Dodaj**.

    ![TABLEAU Online na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Przypisywanie użytkowników do usługi Online firmy Tableau

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączanie automatycznej aprowizacji użytkowników, należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do usługi Online firmy Tableau. Aby przypisać tych użytkowników lub grup do usługi Online firmy Tableau, postępuj zgodnie z instrukcjami [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Online firmy Tableau

*   Firma Microsoft zaleca, aby przypisać jeden użytkownik usługi Azure AD Tableau online, aby przetestować automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników lub grup.

*   Po użytkownik zostanie przypisany do usługi Online firmy Tableau, zaznacz wszystkie prawidłową rolą specyficzne dla aplikacji, jeśli to możliwe, w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurowanie automatycznej aprowizacji użytkownika Tableau Online

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi aprowizacji usługi Azure AD. Umożliwia tworzenie, aktualizowanie i wyłączyć użytkownikom lub grupom w Tableau Online na podstawie użytkownika lub przypisań do grup w usłudze Azure AD.

> [!TIP]
> Ponadto można włączyć opartej na SAML logowanie jednokrotne dla Tableau Online. Postępuj zgodnie z instrukcjami w [Tableau pojedynczego logowania jednokrotnego samouczek Online](tableauonline-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, mimo że te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla Tableau Online w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **Tableau Online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Tableau Online**.

    ![Tableau Online łącze na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![TABLEAU Online aprowizacji](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Tryb aprowizacji TABLEAU Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji, wprowadź domenę, nazwę użytkownika administratora, hasło administratora i adres URL zawartości konta Online firmy Tableau:

   * W **domeny** należy wypełnić w poddomeny, w oparciu o krok 6.

   * W **nazwa użytkownika administratora** pole, wprowadź nazwę użytkownika konta administratora w Twojej dzierżawie Clarizen. Może to być na przykład admin@contoso.com.

   * W **hasło administratora** pole, wprowadź hasło do konta administratora, który odpowiada nazwa użytkownika administratora.

   * W **adres URL zawartości** należy wypełnić w poddomeny, w oparciu o krok 6.

6. Po zalogowaniu Twoje konto administracyjne dla usługi Online firmy Tableau można uzyskać wartości **domeny** i **adres URL zawartości** z adresu URL strony administratora.

    * **Domeny** dla usługi Online firmy Tableau można skopiować konta z tej części adresu URL:

        ![Tableau Online Domain](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **Adres URL zawartości** dla usługi Online firmy Tableau można skopiować konta z tej sekcji. Jest to wartość, która jest zdefiniowana podczas tworzenia konta. W tym przykładzie wartość to "contoso":

        ![Adres URL zawartości Online TABLEAU](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Twoje **domeny** może być różna od przedstawionego w tym miejscu.

7. Po wypełnieniu pól wyświetlane w kroku 5 wybierz **Testuj połączenie** aby upewnij się, że usługi Azure AD można połączyć się z Tableau w trybie Online. Jeśli połączenie nie powiedzie się, upewnij się, że konta Online firmy Tableau ma uprawnienia administratora i spróbuj ponownie.

    ![TABLEAU Online Testuj połączenie](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. W **wiadomość E-mail z powiadomieniem** pole, wprowadź adres e-mail osoby lub grupy do odbierania powiadomień błąd inicjowania obsługi administracyjnej. Wybierz **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria** pole wyboru.

    ![Wiadomość E-mail z powiadomieniem Online TABLEAU](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Wybierz pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Tableau**.

    ![Synchronizacja użytkownika Online TABLEAU](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Tableau w trybie Online w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w dokumentacji Online Tableau operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![TABLEAU pasującego użytkownika atrybuty w trybie Online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do Tableau**.

    ![TABLEAU Online grupy synchronizacji](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD Tableau w trybie Online w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w dokumentacji Online Tableau operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![TABLEAU Online pasującego grupy atrybutów](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Aby skonfigurować filtrów określania zakresu, postępuj zgodnie z instrukcjami [zakresu filtru samouczek](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD w inicjowania obsługi dla usługi Online firmy Tableau **ustawienia** sekcji, zmień **stanie aprowizacji** do **na**.

    ![Stan inicjowania obsługi administracyjnej TABLEAU Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Zdefiniuj użytkowników lub grup, które mają aprowizację Tableau online. W **ustawienia** wybierz wartości, które mają w **zakres**.

    ![Zakres Online TABLEAU](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Gdy wszystko będzie gotowe do aprowizowania, wybierz pozycję **Zapisz**.

    ![Zapisz Online TABLEAU:](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej trwa dłużej niż synchronizacje nowsze. Występują one co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. 

Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków, aby raport o działaniach aprowizacji. Raport w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, usługi online firmy Tableau inicjowania obsługi administracyjnej.

Aby uzyskać informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
