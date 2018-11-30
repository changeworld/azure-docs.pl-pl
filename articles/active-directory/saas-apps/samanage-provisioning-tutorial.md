---
title: 'Samouczek: Konfigurowanie Samanage dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory do automatycznego aprowizowania lub cofania aprowizacji kont użytkowników do Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: v-wingf-msft
ms.openlocfilehash: 53b78231dba4f525410f53dc1ae60901c67f043d
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634510"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Samanage dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest pokazują kroki do wykonania w Samanage i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacji i cofania aprowizacji użytkowników i/lub grup w celu Samanage.

> [!NOTE]
> W tym samouczku opisano łącznika, który został zbudowany na podstawie usługi aprowizacji użytkownika usługi Azure AD. Ważne szczegółowe informacje na temat tej usługi nie, jak działa i często zadawane pytania, [Automatyzowanie aprowizacji użytkowników i anulowania obsługi do aplikacji SaaS w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz już następujące czynności:

*   Dzierżawa usługi Azure AD
*   A [dzierżawy Samanage](https://www.samanage.com/pricing/) pakietem Professional
*   Konto użytkownika Samanage z uprawnieniami administratora

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API Rest Samanage](https://www.samanage.com/api/), który jest dostępny dla deweloperów Samanage dla kont z profesjonalnych pakietu.

## <a name="adding-samanage-from-the-gallery"></a>Dodawanie Samanage z galerii
Przed skonfigurowaniem Samanage dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD, musisz dodać Samanage z galerii aplikacji usługi Azure AD z listą zarządzanych aplikacji SaaS.

**Aby dodać Samanage z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Aplikacje w przedsiębiorstwie sekcji][2]

3. Aby dodać Samanage, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Samanage**.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/AppSearch.png)

5. W panelu wyników wybierz **Samanage**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać Samanage do listy aplikacji SaaS.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/AppSearchResults.png)

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-samanage"></a>Przypisywanie użytkowników do Samanage

Usługa Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników i/lub grup, które "przypisano" do aplikacji w usłudze Azure AD.

Przed Skonfiguruj i Włącz automatyczne aprowizowanie użytkowników, należy zdecydować, użytkowników i/lub grup w usłudze Azure AD muszą mieć dostęp do Samanage. Po decyzję, możesz przypisać użytkownikom i/lub grup Samanage zgodnie z instrukcjami w tym miejscu:

*   [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Samanage

*   Zalecane jest jeden użytkownik usługi Azure AD jest przypisane do Samanage do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników i/lub grup.

*   Podczas przypisywania użytkowników do Samanage, należy wybrać prawidłową rolą specyficzne dla aplikacji (jeśli jest dostępny) w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Konfigurowanie automatycznej aprowizacji użytkowników do Samanage

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi Azure AD inicjowania obsługi usługi do tworzenia, aktualizacji i wyłączanie użytkowników i/lub grup w Samanage oparciu o przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Można też włączyć opartej na SAML logowania jednokrotnego dla Samanage, wykonując instrukcje podane w [Samanage pojedynczego logowania jednokrotnego samouczek](samanage-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, że te dwie funkcje uzupełnienie siebie nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Aby skonfigurować automatyczna aprowizacja użytkowników dla Samanage w usłudze Azure AD:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz Samanage z listy aplikacji SaaS.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/AppInstanceSearch.png)

3. Wybierz **aprowizacji** kartę.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Samanage inicjowania obsługi administracyjnej.](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji danych wejściowych **nazwa użytkownika administratora** i **hasło administratora** Samanage konta. Przykłady te wartości są:

    *   W **nazwa użytkownika administratora** pola, wypełnij nazwę użytkownika konta administratora w Twojej dzierżawie Samanage. Przykład: admin@contoso.com.

    *   W **hasło administratora** pola, wypełnij hasło konta administratora, odpowiadający nazwa użytkownika administratora.

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

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
