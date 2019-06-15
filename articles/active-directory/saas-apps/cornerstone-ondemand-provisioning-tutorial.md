---
title: 'Samouczek: Konfigurowanie OnDemand podstawy dla automatycznej aprowizacji użytkowników z usługą Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługi Azure Active Directory, aby automatycznie aprowizować i anulować aprowizację kont użytkowników do OnDemand podstawy.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721936"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie OnDemand podstawy dla automatycznej aprowizacji użytkowników

Ten samouczek przedstawia kroki do wykonania w OnDemand podstawy i usługi Azure Active Directory (Azure AD), aby skonfigurować usługę Azure AD automatycznie aprowizacja i anulowanie aprowizacji użytkowników lub grup do OnDemand podstawy.

> [!NOTE]
> W tym samouczku opisano łącznika, który bazuje na usłudze usługa aprowizowania użytkowników w usłudze Azure AD. Aby uzyskać informacji na temat tej usługi nie, jak działa i często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i anulowania obsługi aplikacjom software-as-a-service (SaaS) w usłudze Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku przyjęto założenie, że masz:

* Dzierżawa usługi Azure AD.
* Dzierżawca OnDemand podstawy.
* Konto użytkownika w OnDemand podstawy z uprawnieniami administratora.

> [!NOTE]
> Inicjowanie obsługi administracyjnej integracji usługi Azure AD opiera się na [OnDemand podstawy usługi sieci web](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Ta usługa jest dostępna dla zespołów OnDemand podstawy.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Dodaj OnDemand podstawy z witryny Azure Marketplace

Przed skonfigurowaniem OnDemand podstawy dla użytkownika automatyczne Inicjowanie obsługi administracyjnej z usługą Azure AD należy dodać OnDemand podstawy z portalu Marketplace z listą zarządzanych aplikacji SaaS.

Aby dodać OnDemand podstawy z witryny Marketplace, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **OnDemand podstawy** i wybierz **OnDemand podstawy** z panelu wyników. Aby dodać aplikację, wybierz **Dodaj**.

    ![Aplikacja Cornerstone OnDemand na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Przypisywanie użytkowników do OnDemand podstawy

Usługa Azure Active Directory korzysta z koncepcji, o nazwie *przypisania* ustalenie, użytkowników, którzy otrzymają dostęp do wybranych aplikacji. W kontekście automatyczna aprowizacja użytkowników są synchronizowane tylko użytkowników lub grup, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączanie automatycznej aprowizacji użytkowników, należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD muszą mieć dostęp do OnDemand podstawy. Aby przypisać OnDemand podstawy tych użytkowników lub grup, postępuj zgodnie z instrukcjami [przypisać użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Ważne wskazówki dotyczące przypisywania użytkowników do OnDemand podstawy

* Firma Microsoft zaleca, aby przypisać jeden użytkownik usługi Azure AD do OnDemand podstawy do testowania automatyczne aprowizowanie konfiguracji użytkowników. Później można przypisać dodatkowych użytkowników lub grup.

* Po użytkownik jest przypisany do OnDemand podstawy, zaznacz wszystkie prawidłową rolą specyficzne dla aplikacji, jeśli to możliwe, w oknie dialogowym przydział. Użytkownicy z **domyślnego dostępu** roli są wyłączone, od zainicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurowanie automatycznej aprowizacji użytkowników do OnDemand podstawy

Ta sekcja przeprowadzi Cię przez kroki, aby skonfigurować usługi aprowizacji usługi Azure AD. Umożliwia tworzenie, aktualizowanie i wyłączyć użytkownikom lub grupom w OnDemand podstawy na podstawie użytkownika lub przypisań do grup w usłudze Azure AD.

Aby skonfigurować automatyczna aprowizacja użytkowników dla OnDemand podstawy w usłudze Azure AD, wykonaj następujące kroki.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **OnDemand podstawy**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **OnDemand podstawy**.

    ![Link OnDemand podstawy na liście aplikacji](common/all-applications.png)

3. Wybierz **aprowizacji** kartę.

    ![Inicjowanie obsługi administracyjnej OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb obsługi administracyjnej** do **automatyczne**.

    ![Tryb aprowizacji OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. W obszarze **poświadczeń administratora** sekcji, wprowadź nazwę użytkownika administratora, hasło administratora i domenę konta usługi OnDemand podstawy:

    * W **nazwa użytkownika administratora** należy wypełnić w domenie lub nazwa użytkownika konta administratora w Twojej dzierżawie OnDemand podstawy. Przykładem jest contoso\admin.

    * W **hasło administratora** pole, wprowadź hasło, które odnosi się do nazwy użytkownika administratora.

    * W **domeny** pole, wprowadź adres URL usługi sieci web dzierżawy OnDemand podstawy. Na przykład usługa znajduje się w `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, a domena firmy Contoso to `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Aby uzyskać więcej informacji na temat sposobu pobierania adresu URL usługi sieci web, zobacz [ta](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Po wypełnieniu pól wyświetlane w kroku 5 wybierz **Testuj połączenie** aby upewnij się, że usługi Azure AD można połączyć się z OnDemand podstawy. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto OnDemand podstawy ma uprawnienia administratora i spróbuj ponownie.

    ![Połączenie testowe OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. W **wiadomość E-mail z powiadomieniem** pole, wprowadź adres e-mail osoby lub grupy do odbierania powiadomień błąd inicjowania obsługi administracyjnej. Wybierz **Wyślij wiadomość e-mail z powiadomieniem, gdy wystąpi awaria** pole wyboru.

    ![Wiadomość E-mail z powiadomieniem OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Wybierz pozycję **Zapisz**.

9. W obszarze **mapowania** zaznacz **synchronizacji Azure użytkownicy usługi Active Directory do OnDemand podstawy**.

    ![Synchronizacja OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD OnDemand podstawy w **mapowania atrybutów** sekcji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania kont użytkowników w OnDemand podstawy dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Mapowania atrybutów OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Aby skonfigurować filtrów określania zakresu, postępuj zgodnie z instrukcjami [zakresu filtru samouczek](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługi Azure AD, usługi dla OnDemand podstawy inicjowania obsługi administracyjnej w **ustawienia** sekcji, zmień **stanie aprowizacji** do **na**.

    ![Stan inicjowania obsługi administracyjnej OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Zdefiniować użytkowników lub grup, które chcesz, aby obsługiwać je na OnDemand podstawy. W **ustawienia** wybierz wartości, które mają w **zakres**.

    ![Zakres OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Gdy wszystko będzie gotowe do aprowizowania, wybierz pozycję **Zapisz**.

    ![Zapisz OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Ta operacja uruchamia początkowa synchronizacja wszystkich użytkowników lub grup zdefiniowanych w **zakres** w **ustawienia** sekcji. Synchronizacji początkowej trwa dłużej niż synchronizacje nowsze. Występują one co około 40 minut, tak długo, jak działa usługa aprowizacji usługi Azure AD. 

Możesz użyć **szczegóły synchronizacji** sekcji, aby monitorować postęp i skorzystaj z linków, aby raport o działaniach aprowizacji. Raport w tym artykule opisano wszystkie akcje wykonywane przez usługę Azure AD, inicjowania obsługi usługi na OnDemand podstawy.

Aby uzyskać informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

OnDemand podstawy **pozycji** atrybut oczekuje, że wartość, która odnosi się do ról w portalu OnDemand podstawy. Aby uzyskać listę prawidłowe **pozycji** wartości, przejdź do **edytowanie rekordu użytkownika > struktury organizacji > pozycji** w portalu OnDemand podstawy.

![Inicjowanie obsługi administracyjnej OnDemand podstawy edytowanie rekordu użytkownika](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Pozycja aprowizacji OnDemand podstawy](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Podstawa OnDemand inicjowania obsługi administracyjnej pozycja listy](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją konta użytkownika dla aplikacji przedsiębiorstwa](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak przeglądać dzienniki i Uzyskaj raporty dotyczące inicjowania obsługi administracyjnej działania](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
