---
title: 'Samouczek: Konfigurowanie programu Cisco Webex do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego aprowizowania i deekprymarowania kont użytkowników w witrynie Cisco Webex.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77058521"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Cisco Webex do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w witrynie Cisco Webex i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego inicjowania obsługi administracyjnej i deekwowania użytkowników do usługi Cisco Webex.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawca Cisco Webex](https://www.webex.com/pricing/index.html).
* Konto użytkownika w cisco Webex z uprawnieniami administratora.

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie aplikacji Cisco Webex z galerii

Przed skonfigurowaniem usługi Cisco Webex do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać usługę Cisco Webex z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Cisco Webex z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **Cisco Webex**, wybierz pozycję **Cisco Webex** w panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja Cisco Webex na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Przypisywanie użytkowników do cisco Webex

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkownicy powinni zdecydować, którzy użytkownicy w usłudze Azure AD potrzebują dostępu do usługi Cisco Webex. Po podjęciu decyzji, można przypisać tych użytkowników do Cisco Webex, postępując zgodnie z instrukcjami tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Ważne wskazówki dotyczące przypisywania użytkowników do cisco Webex

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do usługi Cisco Webex w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy mogą być przypisani później.

* Podczas przypisywania użytkownika do programu Cisco Webex należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej w witrynie Cisco Webex

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników w programie Cisco Webex na podstawie przypisań użytkowników w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla usługi Cisco Webex w usłudze Azure AD:

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję Wszystkie **aplikacje**, a następnie wybierz pozycję **Cisco Webex**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Cisco Webex**.

    ![Link aplikacji Cisco Webex na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Inicjowanie obsługi administracyjnej usługi Cisco Webex](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowanie obsługi administracyjnej usługi Cisco Webex](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź **adres URL dzierżawy**i **tajny token** konta Cisco Webex.

    ![Inicjowanie obsługi administracyjnej usługi Cisco Webex](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  W polu **Adres URL dzierżawy** wprowadź `https://api.ciscoweb.com/v1/scim/[OrgId]`wartość w postaci . Aby `[OrgId]`uzyskać , zaloguj się do [centrum sterowania Cisco Webex](https://admin.webex.com/login)Control Hub . Kliknij nazwę organizacji w lewym dolnym rogu i skopiuj ją z **identyfikatora organizacji**. 

    * Aby uzyskać wartość **tajnego tokenu,** przejdź do tego [adresu URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Na wyświetlona strona logowania webex zaloguj się przy za pomocą pełnego konta administratora Cisco Webex w organizacji. Pojawi się strona błędu z informacją, że nie można osiągnąć witryny, ale jest to normalne.

        ![Inicjowanie obsługi administracyjnej usługi Cisco Webex](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Skopiuj wartość wygenerowanego tokenu nośnika z adresu URL, jak wyróżniono poniżej. Ten token jest ważny przez 365 dni.
        
        ![Inicjowanie obsługi administracyjnej usługi Cisco Webex](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Po zapełnieniu pól wyświetlanych w kroku 5 kliknij przycisk **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Cisco Webex. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Cisco Webex ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z programem Cisco Webex**.

    ![Inicjowanie obsługi administracyjnej usługi Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do cisco Webex w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w aplikacji Cisco Webex do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Inicjowanie obsługi administracyjnej usługi Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla usługi Cisco Webex, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić aplikacji Cisco Webex, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w witrynie Cisco Webex.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* Cisco Webex jest obecnie w fazie wczesnego testowania pola (EFT) firmy Cisco. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej Firmy Cisco.](https://www.webex.co.in/support/support-overview.html) 
* Więcej informacji na temat konfiguracji programu Cisco Webex można znaleźć w dokumentacji cisco [tutaj.](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)