---
title: 'Samouczek: Konfigurowanie usługi Zendesk do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego udostępniania i usuwania kont użytkowników usługi Zendesk.
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
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062753"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zendesk do automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tym samouczku przedstawiono kroki do wykonania w zendesk i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników i grup do usługi Zendesk.

> [!NOTE]
> W tym samouczku opisano łącznik, który jest zbudowany na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz:

* Dzierżawa usługi Azure AD.
* Dzierżawca Zendesk z planem Professional lub lepiej włączony.
* Konto użytkownika w zendesk z uprawnieniami administratora.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Dodawanie zendesk z portalu Azure Marketplace

Przed skonfigurowaniem usługi Zendesk do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać usługę Zendesk z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać zendesk z Marketplace, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Ikona usługi Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **zendesk** i wybierz **Zendesk** z panelu wyników. Aby dodać aplikację, wybierz pozycję **Dodaj**.

    ![Aplikacja Zendesk na liście wyników](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Przypisywanie użytkowników do zendesk

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do usługi Zendesk. Aby przypisać tych użytkowników lub grupy do zendesk, postępuj zgodnie z instrukcjami w [aplikacji Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Zendesk

* Obecnie role Zendesk są automatycznie i dynamicznie wypełniane w interfejsie użytkownika witryny azure portal. Przed przypisaniem ról Zendesk do użytkowników upewnij się, że synchronizacja początkowa została zakończona z usługą Zendesk w celu pobrania najnowszych ról w dzierżawie Zendesk.

* Zaleca się przypisanie jednego użytkownika usługi Azure AD do zendesk, aby przetestować początkową konfigurację automatycznego inicjowania obsługi administracyjnej użytkownika. Można przypisać dodatkowych użytkowników lub grup później po pomyślnym zakończeniu testów.

* Po przypisaniu użytkownika do usługi Zendesk wybierz dowolną prawidłową rolę specyficzną dla aplikacji, jeśli jest dostępna, w oknie dialogowym przypisywania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w u usług Zendesk 

W tej sekcji znajdziesz instrukcje konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD. Służy do tworzenia, aktualizowania i wyłączania użytkowników lub grup w zendesk na podstawie przypisania użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na saml dla Zendesk. Postępuj zgodnie z instrukcjami zawartymi w [samouczku logowania jednokrotnego Zendesk](zendesk-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla usługi Zendesk w usłudze Azure AD

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz **aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje** > **Zendesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Zendesk**.

    ![Łącze Zendesk na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Inicjowanie obsługi administracyjnej zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Tryb inicjowania obsługi administracyjnej Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. W sekcji **Poświadczenia administratora** wprowadź nazwę użytkownika administratora, tajny token i domenę konta Zendesk. Przykładami tych wartości są:

   * W polu **Nazwa użytkownika administratora** wpisz nazwę użytkownika konta administratora w dzierżawie Zendesk. Może to być na przykład admin@contoso.com.

   * W polu **Tajny token** wypełnij tajny token zgodnie z opisem w kroku 6.

   * W polu **Domena** wypełnij poddomenę dzierżawy Zendesk. Na przykład dla konta z adresem `https://my-tenant.zendesk.com`URL dzierżawy, twoim poddomeną jest **moja dzierżawca.**

6. Tajny token dla twojego konta Zendesk znajduje się w**ustawieniach****interfejsu API** >  **administratora** > . Upewnij się, że **dostęp do tokena** jest ustawiony na **Włączone**.

    ![Ustawienia administratora zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Tajny token Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po wypełnieniu pól wyświetlanych w kroku 5 wybierz **opcję Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Zendesk. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Zendesk ma uprawnienia administratora i spróbuj ponownie.

    ![Połączenie testowe Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, aby otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej. Zaznacz pole wyboru **Wyślij powiadomienie e-mail w** przypadku wystąpienia błędu.

    ![Wiadomość e-mail z powiadomieniem z zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Wybierz **pozycję Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z usługą Zendesk**.

    ![Synchronizacja użytkowników Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Zendesk w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Zendesk do operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Zendesk dopasowujących atrybuty użytkownika](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z usługą Zendesk**.

    ![Synchronizacja grupy Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Zendesk w sekcji **Mapowania atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Zendesk do operacji aktualizacji. Aby zapisać zmiany, wybierz pozycję **Zapisz**.

    ![Zendesk pasujące atrybuty grupy](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Aby skonfigurować filtry zakresu, postępuj zgodnie z instrukcjami zawartymi w [samouczku filtrowania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla Zendesk, w sekcji **Ustawienia** zmień **stan inicjowania obsługi administracyjnej** **na Włączone**.

    ![Stan inicjowania obsługi administracyjnej zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić Zendesk. W sekcji **Ustawienia** wybierz wartości, które chcesz **zaznaczyć**w polu .

    ![Zakres zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Gdy będziesz gotowy do aprowienia, wybierz pozycję **Zapisz**.

    ![Zendesk Zapisz](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż później synchronizuje. Występują one co około 40 minut, tak długo, jak usługa inicjowania obsługi administracyjnej usługi Azure AD jest uruchamiana. 

Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej. Raport opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w zendesk.

Aby uzyskać informacje na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Zendesk obsługuje korzystanie z grup tylko dla użytkowników z rolami **agenta.** Więcej informacji można znaleźć w [dokumentacji zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Gdy rola niestandardowa jest przypisana do użytkownika lub grupy, usługa automatycznego inicjowania obsługi administracyjnej usługi Azure AD przypisuje również domyślną rolę **Agent .** Tylko agentom można przypisać rolę niestandardową. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowewaniem kont użytkowników dla aplikacji przedsiębiorstwa](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
