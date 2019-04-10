---
title: 'Samouczek: Konfigurowanie Samanage dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Samanage.
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
ms.openlocfilehash: ca43b62e66e3a736aa52fdd10fe36e635daba245
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280353"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Samanage dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Samanage i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Samanage.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* A [dzierżawy Samanage](https://www.samanage.com/pricing/) pakietem Professional
* Konto użytkownika Samanage z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API Rest Samanage](https://www.samanage.com/api/), który jest dostępny dla deweloperów Samanage dla kont z profesjonalnych pakietu.

## <a name="adding-samanage-from-the-gallery"></a>Dodawanie usługi Samanage z galerii

Przed skonfigurowaniem Samanage dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać Samanage z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Samanage z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Samanage**, wybierz pozycję **Samanage** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Samanage na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-samanage"></a>Przypisywanie użytkowników do Samanage

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Samanage. Po decyzję, możesz przypisać użytkownikom i/lub grup Samanage zgodnie z instrukcjami w tym miejscu:

*   [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Samanage

*    Role Samanage są dynamiczne i automatycznie uzupełniane w witrynie Azure portal, interfejsu użytkownika już dziś. Przed przypisaniem Samanage ról do użytkowników, upewnij się, że synchronizacja początkowa jest finalizowane przy użyciu Samanage do pobierania najnowszych role w swojej dzierżawie Samanage.

*    Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Samanage do przetestowania Twojej początkowej automatyczne aprowizowanie użytkowników konfiguracji. Dodatkowych użytkowników i/lub grup można przypisać później po pomyślnych testów.

*   Podczas przypisywania użytkowników do Samanage, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Samanage

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Samanage oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Samanage, wykonując instrukcje podane w [Samanage pojedynczego logowania jednokrotnego samouczek](samanage-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Samanage w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Samanage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Samanage**.

    ![Link usługi Samanage na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **nazwa użytkownika administratora** i **hasło administratora** Samanage konta. Przykłady te wartości są:

   * W **nazwa użytkownika administratora** pola, wypełnij nazwę użytkownika konta administratora w Twojej dzierżawie Samanage. Przykład: admin@contoso.com.

   * W **hasło administratora** pola, wypełnij hasło konta administratora, odpowiadający nazwa użytkownika administratora.

6. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Samanage. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Samanage ma uprawnienia administratora i spróbuj ponownie.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/TestConnection.png)

7. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Kliknij pozycję **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Samanage**.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Samanage w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Samanage operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Aby włączyć mapowania grupy w obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do Samanage**.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Ustaw **włączone** do **tak** do grup synchronizacji. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD Samanage w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Samanage operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługi Azure AD, usługi dla Samanage inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Definiowanie użytkowników i/lub grup, które chcesz, aby obsługiwać je na Samanage, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji. Podczas wybierania **Synchronizuj wszystkich użytkowników i grup** opcji, należy wziąć pod uwagę ograniczenia zgodnie z opisem w **ograniczenia łącznika** poniższej sekcji.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Samanage aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Jeśli **Synchronizuj wszystkich użytkowników i grup** opcja jest zaznaczona, a wartość domyślna jest skonfigurowana dla Samanage **role** atrybut, upewnij się, że żądaną wartość w obszarze **wartość domyślna, jeśli ma wartość null (to opcjonalnie)** pole danych jest wyrażona w następującym formacie **{"displayName": "rolę"}** gdzie roli jest wartością domyślną.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
