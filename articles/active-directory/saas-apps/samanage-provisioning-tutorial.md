---
title: 'Samouczek: Konfigurowanie Samanage dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub anulować aprowizację kont użytkowników do Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d474d9bfd6016885eaa21afcea5d44d39c624084
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730590"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Samanage dla automatycznej aprowizacji użytkowników

Ten samouczek przedstawia kroki do wykonania w Samanage i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacja i anulowanie aprowizacji użytkowników i grup do Samanage.

> [!NOTE]
> W tym samouczku opisano łącznika, który bazuje na usłudze usługa aprowizowania użytkowników w usłudze Azure AD. Aby uzyskać informacji na temat tej usługi nie, jak działa i często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i anulowania obsługi aplikacjom software-as-a-service (SaaS) w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz:

* Dzierżawa usługi Azure AD.
* A [dzierżawy Samanage](https://www.samanage.com/pricing/) pakietem Professional.
* Konto użytkownika w Samanage z uprawnieniami administratora.

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API Rest Samanage](https://www.samanage.com/api/). Ten interfejs API jest dostępna dla deweloperów Samanage dla kont z profesjonalnych pakietu.

## <a name="add-samanage-from-the-azure-marketplace"></a>Dodaj Samanage z witryny Azure Marketplace

Przed skonfigurowaniem Samanage dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD należy dodać Samanage w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS.

Aby dodać Samanage z witryny Marketplace, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Samanage** i wybierz **Samanage** z panelu wyników. Aby dodać aplikację, wybierz **Dodaj**.

    ![Samanage na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Przypisywanie użytkowników do Samanage

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączanie automatycznej aprowizacji użytkowników, należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do Samanage. Aby przypisać Samanage tych użytkowników lub grup, postępuj zgodnie z instrukcjami [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Samanage

*    Obecnie Samanage role są dynamiczne i automatycznie uzupełniane w witrynie Azure portal, interfejsu użytkownika. Przed przypisaniem Samanage ról do użytkowników, upewnij się, że synchronizacja początkowa jest finalizowane przy użyciu Samanage do pobierania najnowszych role w swojej dzierżawie Samanage.

*    Firma Microsoft zaleca, aby przypisać jeden użytkownik usługi Azure AD do Samanage do przetestowania Twojej początkowej automatyczne aprowizowanie użytkowników konfiguracji. Możesz przypisać dodatkowych użytkowników i grup później po pomyślnych testów.

*    Po użytkownik jest przypisany do Samanage, zaznacz wszystkie prawidłową rolą specyficzne dla aplikacji, jeśli to możliwe, w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Samanage

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi aprowizacji usługi Azure AD. Umożliwia tworzenie, aktualizowanie i wyłączyć użytkowników lub grup w oparciu o przypisania użytkownika lub grupy w usłudze Azure AD Samanage.

> [!TIP]
> Ponadto można włączyć opartej na SAML logowania jednokrotnego dla Samanage. Postępuj zgodnie z instrukcjami w [Samanage pojedynczego logowania jednokrotnego samouczek](samanage-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, mimo że te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla Samanage w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **Samanage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Samanage**.

    ![Link Samanage na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Tryb aprowizacji Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji, wprowadź nazwę użytkownika administratora i hasło administratora konta Samanage. Przykłady te wartości są:

   * W **nazwa użytkownika administratora** pole, wprowadź nazwę użytkownika konta administratora w Twojej dzierżawie Samanage. Może to być na przykład admin@contoso.com.

   * W **hasło administratora** pole, wprowadź hasło do konta administratora, który odpowiada nazwa użytkownika administratora.

6. Po wypełnieniu pól wyświetlane w kroku 5 wybierz **Testuj połączenie** aby upewnij się, że usługi Azure AD można połączyć się z Samanage. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Samanage ma uprawnienia administratora i spróbuj ponownie.

    ![Połączenie testowe Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. W **wiadomość E-mail z powiadomieniem** pole, wprowadź adres e-mail osoby lub grupy do odbierania powiadomień błąd inicjowania obsługi administracyjnej. Wybierz **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria** pole wyboru.

    ![Wiadomość E-mail z powiadomieniem Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Wybierz pozycję **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Samanage**.

    ![Synchronizacja użytkownika Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Samanage w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Samanage operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Samanage pasujące atrybuty użytkownika](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Aby włączyć mapowania grupy w obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do Samanage**.

    ![Samanage grupy synchronizacji](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Ustaw **włączone** do **tak** do grup synchronizacji. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD Samanage w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Samanage operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Samanage pasujące atrybuty grupy](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Aby skonfigurować filtrów określania zakresu, postępuj zgodnie z instrukcjami [zakresu filtru samouczek](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługi Azure AD, usługi dla Samanage, inicjowania obsługi administracyjnej w **ustawienia** sekcji, zmień **stanie aprowizacji** do **na**.

    ![Stan inicjowania obsługi administracyjnej Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Aby obsługiwać je na Samanage, należy zdefiniować użytkowników lub grup, które chcesz. W **ustawienia** wybierz wartości, które mają w **zakres**. Po wybraniu **Synchronizuj wszystkich użytkowników i grup** opcji, należy wziąć pod uwagę ograniczenia zgodnie z opisem w poniższej sekcji "Łącznik ograniczenia".

    ![Zakres Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Gdy wszystko będzie gotowe do aprowizowania, wybierz pozycję **Zapisz**.

    ![Zapisz Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej trwa dłużej niż synchronizacje nowsze. Występują one co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. 

Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków, aby raport o działaniach aprowizacji. Raport w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD usługi na Samanage inicjowania obsługi administracyjnej.

Aby uzyskać informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

Jeśli wybierzesz **Synchronizuj wszystkich użytkowników i grup** opcji i skonfigurować wartość dla Samanage **role** atrybutu wartości w **wartość domyślna, jeśli ma wartość null (jest opcjonalny)** okno muszą być wyrażone w następującym formacie:

- {"displayName": "rolę"}, w którym rola jest wartość domyślna ma.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
