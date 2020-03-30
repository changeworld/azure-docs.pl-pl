---
title: 'Samouczek: Konfigurowanie usługi Tableau Online w celu automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników w usłudze Tableau Online.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064222"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi Tableau Online do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku przedstawiono kroki do wykonania w tableau online i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników i grup do usługi Tableau Online.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz:

*   Dzierżawa usługi Azure AD.
*   [Tableau Online dzierżawy](https://www.tableau.com/).
*   Konto użytkownika w tableau online z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na [interfejsie API spoczynku online Tableau](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Ten interfejs API jest dostępny dla deweloperów usługi Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Dodawanie tableau online z portalu Azure Marketplace
Przed skonfigurowaniem usługi Tableau Online do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać usługę Tableau Online z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać tableau online z marketplace, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **tableau online** i wybierz **tableau online** z panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Tableau Online na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Przypisywanie użytkowników do usługi Tableau Online

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej, zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Usługi Tableau Online. Aby przypisać tych użytkowników lub grupy do usługi Tableau Online, postępuj zgodnie z instrukcjami w obszarze [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Tableau Online

*   Zaleca się przypisanie jednego użytkownika usługi Azure AD do usługi Tableau Online w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Później można przypisać dodatkowych użytkowników lub grupy.

*   Po przypisaniu użytkownika do usługi Tableau Online wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisywania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w uliczce Tableau Online

W tej sekcji znajdziesz instrukcje konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w tableau online na podstawie przypisania użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samol oparte logowanie jednokrotne dla Tableau Online. Postępuj zgodnie z instrukcjami w [tableau online logowania jednokrotnego samouczka](tableauonline-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla usługi Tableau Online w usłudze Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje** > **Tableau Online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Tableau Online**.

    ![Łącze Tableau Online na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Aprowerowowanie online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Tryb inicjowania obsługi administracyjnej usługi Tableau online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. W sekcji **Poświadczenia administratora** wprowadź domenę, nazwę użytkownika administratora, hasło administratora i adres URL zawartości konta Tableau Online:

   * W polu **Domena** wypełnij poddomenę na podstawie kroku 6.

   * W polu **Nazwa użytkownika administratora** wpisz nazwę użytkownika konta administratora w dzierżawie Clarizen. Może to być na przykład admin@contoso.com.

   * W polu **Hasło administratora** wprowadź hasło konta administratora odpowiadające nazwie użytkownika administratora.

   * W polu **Adres URL zawartości** wypełnij poddomenę na podstawie kroku 6.

6. Po zalogowaniu się do konta administracyjnego w aplikacji Tableau Online wartości **adresu URL** **domeny** i zawartości można uzyskać z adresu URL strony administratora.

    * **Domenę** konta Tableau Online można skopiować z tej części adresu URL:

        ![Domena online Tableau](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Adres **URL zawartości** konta Tableau Online można skopiować z tej sekcji. Jest to wartość zdefiniowana podczas konfigurowania konta. W tym przykładzie wartość to "contoso":

        ![Adres URL zawartości online Tableau](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Twoja **domena** może się różnić od tej pokazanej tutaj.

7. Po wypełnieniu pól wyświetlanych w kroku 5 wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Tableau Online. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Tableau Online ma uprawnienia administratora i spróbuj ponownie.

    ![Połączenie testowe tableau online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Zaznacz pole wyboru **Wyślij powiadomienie e-mail w** przypadku wystąpienia błędu.

    ![Tableau Online Powiadomienie e-mail](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Wybierz **pozycję Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z tableau**.

    ![Synchronizacja użytkowników Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Tableau Online w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Tableau Online dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Tableau Online pasujące atrybuty użytkownika](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z tabelą**.

    ![Synchronizacja grup Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Tableau Online w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Tableau Online dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Tableau Online pasujące atrybuty grupy](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtrowania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi Tableau Online, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

    ![Stan aprowizacji online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić tableau online. W sekcji **Ustawienia** wybierz wartości, które chcesz **zaznaczyć**w polu .

    ![Zakres online Tableau](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Tableau Zapisywanie online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż później synchronizuje. Występują one co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchamiana. 

Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej. Raport opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w tableau online.

Aby uzyskać informacje na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
