---
title: 'Samouczek: Konfigurowanie Dropbox dla firm do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i deekwowania kont użytkowników w Dropbox dla Firm.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058452"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Dropbox dla firm do automatycznego inicjowania obsługi administracyjnej użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w Dropbox dla Firm i usługi Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do Dropbox dla firm.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa Dropbox dla Firm](https://www.dropbox.com/business/pricing)
* Konto użytkownika w Dropbox dla Firm z uprawnieniami administratora.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dodawanie Dropbox dla firm z galerii

Przed skonfigurowaniem dropbox dla firm do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać Dropbox dla firm z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Dropbox dla Firm z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Dropbox dla Firm**, wybierz Dropbox dla **Firm** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja Dropbox dla Firm na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Przypisywanie użytkowników do Dropbox dla Firm

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownicy należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Dropbox dla Firm. Po podjęciu decyzji możesz przypisać tych użytkowników i/lub grupy do Dropbox dla Firm, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Dropbox dla Firm

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Dropbox dla firm w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkowników. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Dropbox dla Firm musisz wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w Dropbox dla firm 

W tej sekcji znajdziesz przewodnik po krokach konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Dropbox dla firm na podstawie przypisaniów użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na SAML dla Dropbox dla Firm, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego Dropbox dla firm](dropboxforbusiness-tutorial.md). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla Dropbox dla firm w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Dropbox dla firm**.

    ![Link aplikacji Dropbox dla Firm na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. Otwiera okno logowania dropbox dla firm w nowym oknie przeglądarki.

    ![Inicjowanie obsługi ](common/provisioning-oauth.png)

6. W oknie dialogowym **Logowanie do Dropbox dla Firm w celu połączenia z usługą Azure AD** zaloguj się do dzierżawy Dropbox dla Firm i zweryfikuj swoją tożsamość.

    ![Logowanie do Dropbox dla Firm](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Po wykonaniu kroków 5 i 6 kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z Dropbox dla firm. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto Dropbox dla Firm ma uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-oauth.png)

8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z Dropbox**.

    ![Mapowania użytkowników Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Dropbox w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Dropbox do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkowników Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z Dropbox**.

    ![Mapowania grup Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Dropbox w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Dropbox do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla Dropbox, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Dropbox, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

17. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w Dropbox.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza
 
* Dropbox nie obsługuje zawieszania zaproszonych użytkowników. Jeśli zaproszony użytkownik zostanie zawieszony, zostanie on usunięty.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

