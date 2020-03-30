---
title: 'Samouczek: Konfigurowanie konsoli administratora netskope do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i wyrównywalego udostępniania kont użytkowników w konsoli administratora sieci Netskope.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061293"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie konsoli administratora netskope do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w konsoli administratora netskope i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwzwalowania użytkowników i/lub grup do konsoli administratora sieci Netskope.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa konsoli administratora netskope](https://www.netskope.com/)
* Konto użytkownika w Konsoli administratora netskope z uprawnieniami administratora.

## <a name="assigning-users-to-netskope-administrator-console"></a>Przypisywanie użytkowników do konsoli administratora netskope

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownikom należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do konsoli administratora netskope. Po podjęciu decyzji można przypisać tych użytkowników i/lub grupy do Konsoli administratora netskope, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Ważne wskazówki dotyczące przypisywania użytkowników do konsoli administratora netskope

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do konsoli administratora usługi Netskope w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do konsoli administratora netskope należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Konfigurowanie konsoli administratora sieci Netskope do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej konsoli administratora sieci Netskope](https://netskope.goskope.com/). Przejdź do **ustawień > głównej**.

    ![Konsola administracyjna konsoli administratora netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Przejdź do **pozycji Narzędzia**. W menu **Narzędzia** przejdź do **pozycji Narzędzia katalogowe > SCIM INTEGRATION**.

    ![Narzędzia konsoli administratora netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Administrator Console Dodaj SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Przewiń w dół i kliknij przycisk **Dodaj żeton.** W oknie dialogowym **Dodawanie nazwy klienta OAuth** podaj **NAZWĘ KLIENTA** i kliknij przycisk **Zapisz.**

    ![Netskope Administrator Konsoli Dodaj token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Nazwa konsoli administratora netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Skopiuj **adres URL serwera SCIM** i **token**. Te wartości zostaną wprowadzone odpowiednio w polach Adres URL dzierżawy i Token tajny na karcie Inicjowanie obsługi administracyjnej aplikacji konsoli administratora sieci Netskope w witrynie Azure portal.

    ![Netskope Administrator Console Create Token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Dodawanie konsoli administratora netskope z galerii

Przed skonfigurowaniem konsoli administratora sieci Netskope do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać konsolę administratora netskope z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać konsolę administratora usługi Netskope z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **konsolę administratora netskope**, wybierz **pozycję Netskope Administrator Console** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Netskope Administrator Console na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej konsoli administratora sieci Netskope 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w konsoli administratora netskope na podstawie przypisaniów użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na SAML dla konsoli administratora Netskope, postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego konsoli usługi Netskope Administrator Console](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym SCIM konsoli administratora sieci Netskope, zapoznaj się [z tym](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej konsoli administratora usługi Netskope w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Netskope Administrator Console**.

    ![Łącze Konsoli administratora netskope na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź wartość **adresu URL serwera SCIM** pobraną wcześniej w **adresie URL dzierżawy**. Wprowadź wartość **TOKEN** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z konsolą administratora netskope. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Konsoli administratora netskope ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z konsolą administratora netskope**.

    ![Mapowania użytkowników konsoli administratora netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do konsoli administratora netskope w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w Konsoli administratora netskope do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika konsoli administratora netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z konsolą administratora netskope**.

    ![Mapowania grup konsoli administratora netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do konsoli administratora netskope w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania grup w Konsoli administratora netskope do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy konsoli administratora netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla konsoli administratora usługi Netskope, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić konsoli administratora netskope, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w konsoli administratora sieci Netskope.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

