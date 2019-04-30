---
title: 'Samouczek: Konfigurowanie systemu Zendesk dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie aprowizować i anulować aprowizację kont użytkowników do systemu Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f559d2c2398998ba590419758de559f21d9b65f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114669"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie systemu Zendesk dla automatycznej aprowizacji użytkowników

Ten samouczek przedstawia kroki do wykonania w systemie Zendesk i usługi Azure Active Directory (Azure AD), aby skonfigurować usługi Azure AD automatycznego aprowizowania lub anulować aprowizację użytkowników i grup do systemu Zendesk.

> [!NOTE]
> W tym samouczku opisano łącznika, który bazuje na usłudze usługa aprowizowania użytkowników w usłudze Azure AD. Aby uzyskać informacji na temat tej usługi nie, jak działa i często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i anulowania obsługi aplikacjom software-as-a-service (SaaS) w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz:

* Dzierżawa usługi Azure AD.
* Dzierżawcy systemu Zendesk z [Enterprise](https://www.zendesk.com/product/pricing/) plan lub lepiej jest włączone.
* Konto użytkownika w usłudze Zendesk z uprawnieniami administratora.

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [interfejsu API Rest systemu Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction). Ten interfejs API jest dostępny dla zespołów systemu Zendesk z planem Enterprise lub większą.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Dodaj systemu Zendesk z witryny Azure Marketplace

Przed skonfigurowaniem systemu Zendesk dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD należy dodać systemu Zendesk z witryny Azure Marketplace z listą zarządzanych aplikacji SaaS.

Aby dodać systemu Zendesk z witryny Marketplace, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Zendesk** i wybierz **Zendesk** z panelu wyników. Aby dodać aplikację, wybierz **Dodaj**.

    ![Aplikacja Zendesk na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Przypisywanie użytkowników do systemu Zendesk

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączanie automatycznej aprowizacji użytkowników, należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do systemu Zendesk. Aby przypisać tych użytkowników lub grup do systemu Zendesk, postępuj zgodnie z instrukcjami [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Ważne wskazówki dotyczące przypisywania użytkowników do systemu Zendesk

* Obecnie role systemu Zendesk są dynamiczne i automatycznie uzupełniane w witrynie Azure portal, interfejsu użytkownika. Przed przypisaniem Zendesk ról do użytkowników, upewnij się, że synchronizacja początkowa jest finalizowane przy użyciu systemu Zendesk, aby pobrać najnowsze ról w dzierżawie usługi Zendesk.

* Firma Microsoft zaleca, aby przypisać jeden użytkownik usługi Azure AD do systemu Zendesk, aby przetestować swoje początkowej automatyczne aprowizowanie użytkowników konfiguracji. Możesz przypisać dodatkowych użytkowników lub grup później po pomyślnych testów.

* Po użytkownik jest przypisany do systemu Zendesk, zaznacz wszystkie prawidłową rolą specyficzne dla aplikacji, jeśli to możliwe, w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurowanie automatycznej aprowizacji użytkowników do systemu Zendesk 

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi aprowizacji usługi Azure AD. Umożliwia tworzenie, aktualizowanie i wyłączyć użytkowników lub grup w oparciu o przypisania użytkownika lub grupy w usłudze Azure AD w systemie Zendesk.

> [!TIP]
> Ponadto można włączyć opartej na SAML logowania jednokrotnego dla systemu Zendesk. Postępuj zgodnie z instrukcjami w [Zendesk pojedynczego logowania jednokrotnego samouczek](zendesk-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatyczna aprowizacja użytkowników, mimo że te dwie funkcje uzupełniają się wzajemnie.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla systemu Zendesk w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **Zendesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Zendesk**.

    ![Łącze systemu Zendesk, na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Zendesk Provisioning](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Tryb inicjowania obsługi administracyjnej systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. W obszarze **poświadczeń administratora** sekcji, wprowadź nazwę użytkownika administratora, token wpisu tajnego i domenę konta systemu Zendesk. Przykłady te wartości są:

   * W **nazwa użytkownika administratora** pole, wprowadź nazwę użytkownika konta administratora w dzierżawie usługi Zendesk. Może to być na przykład admin@contoso.com.

   * W **klucz tajny tokenu** należy wypełnić w token wpisu tajnego, zgodnie z opisem w kroku 6.

   * W **domeny** pole, wprowadź domenę podrzędną w dzierżawie usługi Zendesk. Na przykład konto z adresem URL dzierżawy `https://my-tenant.zendesk.com`, Twojej domeny podrzędnej jest **Moje dzierżawy**.

6. Token wpisu tajnego dla konta systemu Zendesk znajduje się w **administratora** > **API** > **ustawienia**. Upewnij się, że **dostęp do tokenu** ustawiono **włączone**.

    ![Ustawienia administratora systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token wpisu tajnego systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po wypełnieniu pól wyświetlane w kroku 5 wybierz **Testuj połączenie** aby upewnij się, że usługi Azure AD można połączyć się z systemu Zendesk. Jeśli połączenie nie powiedzie się, upewnij się, że konta systemu Zendesk, ma uprawnienia administratora i spróbuj ponownie.

    ![Połączenie testowe systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. W **wiadomość E-mail z powiadomieniem** pole, wprowadź adres e-mail osoby lub grupy do odbierania powiadomień błąd inicjowania obsługi administracyjnej. Wybierz **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria** pole wyboru.

    ![Wiadomość E-mail z powiadomieniem systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Wybierz pozycję **Zapisz**.

10. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do systemu Zendesk**.

    ![Synchronizacja użytkownika systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD zendesk w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w usłudze Zendesk dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Pasujące atrybuty użytkownika systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. W obszarze **mapowania** zaznacz **synchronizacji Azure grup usługi Active Directory do systemu Zendesk**.

    ![Synchronizację grupową systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD zendesk w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania grup w usłudze Zendesk dla operacji aktualizowania. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Pasujące atrybuty grupy systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Aby skonfigurować filtrów określania zakresu, postępuj zgodnie z instrukcjami [zakresu filtru samouczek](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługi Azure AD, usługi dla systemu Zendesk, inicjowania obsługi administracyjnej w **ustawienia** sekcji, zmień **stanie aprowizacji** do **na**.

    ![Stan inicjowania obsługi administracyjnej systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zdefiniuj użytkowników lub grup, które chcesz aby obsługiwać je na systemu Zendesk. W **ustawienia** wybierz wartości, które mają w **zakres**.

    ![Zakres systemu Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Gdy wszystko będzie gotowe do aprowizowania, wybierz pozycję **Zapisz**.

    ![Zendesk Save](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej trwa dłużej niż synchronizacje nowsze. Występują one co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. 

Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków, aby raport o działaniach aprowizacji. Raport w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD provisioning service w systemie Zendesk.

Aby uzyskać informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Zendesk obsługuje korzystanie z grup użytkowników usługi **agenta** tylko role. Aby uzyskać więcej informacji, zobacz [dokumentację systemu Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Rola niestandardowa jest przypisany do użytkownika lub grupy, usługi Azure AD automatyczne aprowizowanie użytkowników usługa również przypisuje domyślna rola **agenta**. Rolę niestandardową można przypisać tylko agentów. Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API w systemu Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
