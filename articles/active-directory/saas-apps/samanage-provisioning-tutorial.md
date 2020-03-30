---
title: 'Samouczek: Konfigurowanie samanage do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania kont użytkowników usługi Samanage.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060495"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Samanage do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku przedstawiono kroki do wykonania w samanage i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników i grup na samanage.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Samanage](https://www.samanage.com/pricing/) z pakietem Professional.
* Konto użytkownika w Samanage z uprawnieniami administratora.

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na [interfejsie API odpoczynku Samanage.](https://www.samanage.com/api/) Ten interfejs API jest dostępny dla deweloperów Samanage dla kont z pakietem Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Dodawanie samanage z portalu Azure Marketplace

Przed skonfigurowaniem Samanage do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać samanage z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać Samanage z Marketplace, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **pozycję Samanage** i wybierz **samanage** z panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Samanage na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Przypisywanie użytkowników do Samanage

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownika, zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do Samanage. Aby przypisać tych użytkowników lub grupy do Samanage, postępuj zgodnie z instrukcjami w [aplikacji Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Samanage

*    Obecnie role Samanage są automatycznie i dynamicznie wypełniane w interfejsie użytkownika witryny azure portal. Przed przypisaniem ról Samanage do użytkowników upewnij się, że synchronizacja początkowa została zakończona przeciwko Samanage, aby pobrać najnowsze role w dzierżawie Samanage.

*    Zaleca się przypisanie jednego użytkownika usługi Azure AD do Samanage, aby przetestować początkową konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Można przypisać dodatkowych użytkowników i grup później po pomyślnym zakończeniu testów.

*    Po przypisaniu użytkownika do samanage wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisywania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla samanage

W tej sekcji znajdziesz instrukcje konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w Samanage na podstawie przypisania użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć samol oparte logowanie jednokrotne dla Samanage. Postępuj zgodnie z instrukcjami w [samouczek logowania jednokrotnego Samanage](samanage-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla samanage w usłudze Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **aplikacje dla** > **przedsiębiorstw Wszystkie aplikacje** > **Samanage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Samanage**.

    ![Łącze Samanage na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Inicjowanie obsługi administracyjnej samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy** Samanage i **token tajny**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z samanage. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Samanage ma uprawnienia administratora i spróbuj ponownie.

    ![Połączenie testowe Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Zaznacz pole wyboru **Wyślij powiadomienie e-mail w** przypadku wystąpienia błędu.

    ![Wiadomość e-mail z powiadomieniem o Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Wybierz **pozycję Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z samanage**.

    ![Synchronizacja użytkownika Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Samanage w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Samanage dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Samanage pasujące atrybuty użytkownika](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Aby włączyć mapowanie grup, w sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory na Samanage**.

    ![Synchronizacja grupy Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Ustaw **włączone na** **Tak,** aby synchronizować grupy. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Samanage w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Samanage dla operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Atrybuty grupy dopasowywania samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtrowania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla samanage, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

    ![Stan inicjowania obsługi administracyjnej samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Samanage. W sekcji **Ustawienia** wybierz wartości, które chcesz **zaznaczyć**w polu . Po wybraniu opcji **Synchronizuj wszystkich użytkowników i grup** należy wziąć pod uwagę ograniczenia opisane w poniższej sekcji "Ograniczenia łącznika".

    ![Zakres Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Samanage Zapisz](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż później synchronizuje. Występują one co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchamiana. 

Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej. Raport opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w samanage.

Aby uzyskać informacje na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

Jeśli wybierzesz opcję **Synchronizuj wszystkich użytkowników i grup** i skonfigurujesz wartość **atrybutu Role** Samanage, wartość w polu Wartość **domyślna, jeśli null (jest opcjonalna)** musi być wyrażona w następującym formacie:

- {"displayName":"role"}, gdzie rola jest wartością domyślną.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
