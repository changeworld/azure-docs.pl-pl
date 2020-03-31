---
title: 'Samouczek: Konfigurowanie identyfikatora cornerstone onDemand do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kont użytkowników do Cornerstone OnDemand.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058447"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie cornerstone OnDemand do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku przedstawiono kroki do wykonania w Cornerstone OnDemand i azure active directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników lub grup do Cornerstone OnDemand.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz:

* Dzierżawa usługi Azure AD.
* Najemca Cornerstone OnDemand.
* Konto użytkownika w Cornerstone OnDemand z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na [usługi sieci web Cornerstone OnDemand.](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf) Ta usługa jest dostępna dla zespołów Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Dodaj Cornerstone OnDemand z portalu Azure Marketplace

Przed skonfigurowaniem Cornerstone OnDemand do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać Cornerstone OnDemand z portalu Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać Cornerstone OnDemand z Marketplace, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Cornerstone OnDemand** i wybierz **Cornerstone OnDemand** z panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Aplikacja Cornerstone OnDemand na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Przypisywanie użytkowników do Cornerstone OnDemand

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownika, zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Cornerstone OnDemand. Aby przypisać tych użytkowników lub grupy do Cornerstone OnDemand, postępuj zgodnie z instrukcjami w [Przypisz użytkownika lub grupę do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Cornerstone OnDemand

* Zaleca się przypisanie jednego użytkownika usługi Azure AD do Cornerstone OnDemand, aby przetestować konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Później można przypisać dodatkowych użytkowników lub grupy.

* Po przypisaniu użytkownika do cornerstone OnDemand wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisywania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej do Cornerstone OnDemand

W tej sekcji znajdziesz instrukcje konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w Cornerstone OnDemand na podstawie przypisania użytkowników lub grup w usłudze Azure AD.

Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla Cornerstone OnDemand w usłudze Azure AD, wykonaj następujące kroki.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje** > **Cornerstone OnDemand**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **opcję Cornerstone OnDemand**.

    ![Link Cornerstone OnDemand na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Tworzenie obsługi administracyjnej OnDemand cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **automatyczny**.

    ![Cornerstone OnDemand Tryb inicjowania obsługi administracyjnej](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. W sekcji **Poświadczenia administratora** wprowadź nazwę użytkownika administratora, hasło administratora i domenę konta Cornerstone OnDemand:

    * W polu **Nazwa użytkownika administratora** wpisz domenę lub nazwę użytkownika konta administratora w dzierżawie Cornerstone OnDemand. Przykładem jest contoso\admin.

    * W polu **Hasło administratora** wprowadź hasło odpowiadające nazwie użytkownika administratora.

    * W polu **Domena** wypełnij adres URL usługi sieci web dzierżawy Cornerstone OnDemand. Na przykład usługa znajduje `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`się w punkcie , `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`a dla contoso domeną jest domena . Aby uzyskać więcej informacji na temat pobierania adresu URL usługi sieci Web, zobacz [ten plik pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Po wypełnieniu pól wyświetlanych w kroku 5 wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z elementami podstawowych. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Cornerstone OnDemand ma uprawnienia administratora i spróbuj ponownie.

    ![Kamień węgielny OnDemand Połączenie testowe](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Zaznacz pole wyboru **Wyślij powiadomienie e-mail w** przypadku wystąpienia błędu.

    ![Cornerstone OnDemand powiadomienie e-mail](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Wybierz **pozycję Zapisz**.

9. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem Cornerstone OnDemand**.

    ![Synchronizacja OnDemand kamień węgielny](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Cornerstone OnDemand w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Cornerstone OnDemand dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Mapowania atrybutów OnDemand cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtrowania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla programu Cornerstone OnDemand, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

    ![Stan inicjowania obsługi administracyjnej OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Zdefiniuj użytkowników lub grupy, które mają być aprowizować do Cornerstone OnDemand. W sekcji **Ustawienia** wybierz wartości, które chcesz **zaznaczyć**w polu .

    ![Kamień węgielny OnDemand Zakres](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Kamień węgielny OnDemand Zapisz](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż później synchronizuje. Występują one co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchamiana. 

Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej. Raport opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w Cornerstone OnDemand.

Aby uzyskać informacje na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

Atrybut Cornerstone OnDemand **Position** oczekuje wartości odpowiadającej roli w portalu OnDemand. Aby uzyskać listę prawidłowych wartości **pozycji,** przejdź do pozycji **Edytuj rekord użytkownika > struktury organizacji > pozycję** w portalu OnDemand cornerstone.

![Kamień węgielny OnDemand inicjowania obsługi administracyjnej rekord użytkownika edycji](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand aprowizując stanowisko](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand Aprowizacji listy pozycji](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
