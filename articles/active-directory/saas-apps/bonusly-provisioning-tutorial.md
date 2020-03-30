---
title: 'Samouczek: Konfigurowanie automatycznie inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i usuwania z obsługi konta użytkowników bonusowo.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058879"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie automatycznie inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usłudze Bonusly i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do bonusly.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące czynności:

* Dzierżawa usługi Azure AD
* Najemca [bonusowo](https://bonus.ly/pricing)
* Konto użytkownika w Bonusly z uprawnieniami administratora

> [!NOTE]
> Integracja inicjowania obsługi administracyjnej usługi Azure AD opiera się na [interfejsie API dodatkowej odpoczynku,](https://konghq.com/solutions/gateway/)który jest dostępny dla deweloperów bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Dodawanie bonusu z galerii

Przed skonfigurowaniem bonusly do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać bonusly z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać bonusowo z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Bonusly**, wybierz **Bonusly** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Bonusowo na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Przypisywanie użytkowników do bonusu

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane. 

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do bonusly. Po podjęciu decyzji, możesz przypisać tych użytkowników i/lub grupy do Bonusly, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Bonusly

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do bonusly do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do bonusowo należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w bonusowym

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w bonusly na podstawie przypisania użytkownika i/lub grupy w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć samooceny jednokrotne samooki dla bonusu oparte na SAML, postępując zgodnie z instrukcjami podanymi w [samouczku samouczek logowania jednokrotnego Bonusly.](bonus-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla bonusly w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję Wszystkie **aplikacje**, a następnie wybierz **opcję Bonusly**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **opcję Bonusly**.

    ![Link Bonusly na liście Aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. W sekcji **Poświadczenia administratora** wprowadź **tajny token** swojego konta bonusowego zgodnie z opisem w kroku 6.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. **Tajny token** dla Twojego konta bonusowego znajduje się w **misji Admin > Company > Integrations.** W **sekcji Jeśli chcesz kod,** kliknij na **interfejs API > utwórz nowy token dostępu interfejsu API,** aby utworzyć nowy tajny token.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Na poniższym ekranie wpisz nazwę tokenu dostępu w podanym polu tekstowym, a następnie naciśnij klawisz **Utwórz klawisz Api**. Nowy token dostępu pojawi się przez kilka sekund w wyskakującym okienku.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/Token02.png)

8. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z bonusem. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Bonusly ma uprawnienia administratora i spróbuj ponownie.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu**.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Kliknij przycisk **Zapisz**.

11. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z bonusowo**.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do bonusly w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Bonusly dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla bonusly, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić bonusowo, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Premiowa aprowizacji](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w bonusly.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
