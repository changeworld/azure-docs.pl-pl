---
title: 'Samouczek: Konfigurowanie Bonusly dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad0ee590572dbc92e67be9f84ffc65afc3e8473
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278738"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Bonusly dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Bonusly i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Bonusly.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* A [Bonusly dzierżawy](https://bonus.ly/pricing)
* Konto użytkownika Bonusly z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [Bonusly interfejsu API Rest](https://bonusly.gelato.io/reference), który jest dostępny dla deweloperów Bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Dodawanie Bonusly z galerii

Przed skonfigurowaniem Bonusly dla automatycznej aprowizacji użytkowników z usługą Azure AD, musisz dodać Bonusly z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Bonusly z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Bonusly**, wybierz opcję **Bonusly** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Bonusly na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Przypisywanie użytkowników do Bonusly

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD. 

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Bonusly. Po decyzję, możesz przypisać użytkownikom i/lub grup Bonusly zgodnie z instrukcjami w tym miejscu:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Bonusly

* Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Bonusly do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

* Podczas przypisywania użytkowników do Bonusly, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Bonusly

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Bonusly oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Bonusly, wykonując instrukcje podane w [Bonusly pojedynczego logowania jednokrotnego samouczek](bonus-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Bonusly w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Bonusly**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Bonusly**.

    ![Bonusly łącze na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **klucz tajny tokenu** z Bonusly swoje konto zgodnie z opisem w kroku 6.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. **Klucz tajny tokenu** dla Bonusly Twoje konto znajduje się w **Administrator > firmy > integracje**. W **aby kod** sekcji, kliknij pozycję **interfejs API > Utwórz nowy interfejs API Access Token** Aby utworzyć nowy klucz tajny tokenu.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Na poniższym ekranie, wpisz nazwę dla tokenu dostępu, w polu tekstowym podana naciśnij **Utwórz klucz interfejsu Api**. Nowy token dostępu pojawi się na kilka sekund w oknie podręcznym.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/Token02.png)

8. Podczas wypełniania pola wyświetlane w kroku 5, kliknij przycisk **Testuj połączenie** aby zapewnić usłudze Azure AD można połączyć się z Bonusly. Jeśli połączenie nie powiedzie się, upewnij się, że Bonusly konto ma uprawnienia administratora i spróbuj ponownie.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. W **wiadomość E-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, który powinien otrzymywać powiadomienia błąd inicjowania obsługi administracyjnej i zaznacz pole wyboru **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria**.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Kliknij pozycję **Zapisz**.

11. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do Bonusly**.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD Bonusly w **mapowanie atrybutu** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w Bonusly operacji aktualizacji. Wybierz **Zapisz** przycisk, aby zatwierdzić zmiany.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Aby skonfigurować filtrów określania zakresu, można znaleźć w następujących instrukcjach podanych w [samouczek filtru Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługi Azure AD, usługi dla Bonusly inicjowania obsługi administracyjnej, zmień **stanie aprowizacji** do **na** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definiowanie użytkowników i/lub grup, które chcesz, aby obsługiwać je na Bonusly, wybierając odpowiednie wartości w **zakres** w **ustawienia** sekcji.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Gdy wszystko jest gotowe do aprowizowania, kliknij przycisk **Zapisz**.

    ![Inicjowanie obsługi administracyjnej bonusly](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników i/lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków do raportu działań w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na Bonusly aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
