---
title: 'Samouczek: Konfigurowanie programu Zscaler Private Access (ZPA) do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników do programu Zscaler Private Access (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064159"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Zscaler Private Access (ZPA) do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w programie Zscaler Private Access (ZPA) i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego inicjowania obsługi administracyjnej i deekwowania użytkowników i/lub grup dostępu prywatnego (ZPA).

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa zscaler private access (ZPA)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Konto użytkownika w programie Zscaler Private Access (ZPA) z uprawnieniami administratora.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Przypisywanie użytkowników do programu Zscaler Private Access (ZPA)

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do programu Zscaler Private Access (ZPA). Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do Zscaler Private Access (ZPA), postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Ważne wskazówki dotyczące przypisywania użytkowników do programu Zscaler Private Access (ZPA)

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do programu Zscaler Private Access (ZPA) w celu przetestowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do programu Zscaler Private Access (ZPA) należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Konfigurowanie programu Zscaler Private Access (ZPA) do inicjowania obsługi administracyjnej

1. Zaloguj się do [konsoli administracyjnej Zscaler Private Access (ZPA).](https://admin.private.zscaler.com/) Przejdź do **konfiguracji > IdP administracji**.

    ![Konsola administracyjna Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Sprawdź, czy jest skonfigurowany idP dla **logowania jednokrotnego.** Jeśli nie jest skonfigurowany żaden IdP, dodaj go, klikając ikonę plus w prawym górnym rogu ekranu.

    ![Zscaler Dostęp prywatny (ZPA) Dodaj SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Postępuj zgodnie z **Kreatorem dodawania konfiguracji idp,** aby dodać IdP. Pozostaw pole **logowania jednokrotnego** ustawione na **User**. Podaj **nazwę** i wybierz **domeny** z listy rozwijanej. Kliknij **przycisk Dalej,** aby przejść do następnego okna.

    ![Zscaler Dostęp prywatny (ZPA) Dodaj IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Pobierz **certyfikat usługodawcy**. Kliknij **przycisk Dalej,** aby przejść do następnego okna.

    ![Certyfikat SP zscaler private access (ZPA)](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. W następnym oknie przekaż **certyfikat usługodawcy** pobrany wcześniej.

    ![Certyfikat przekazywania programu Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Przewiń w dół, aby podać **adres URL logowania jednokrotnego** i **identyfikator jednostki IdP**.

    ![Identyfikator IDP Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Przewiń w dół, aby **włączyć synchronizację SCIM**. Kliknij przycisk **Generuj nowy żeton.** Skopiuj **token na okaziciela**. Ta wartość zostanie wprowadzona w polu Token tajny na karcie Inicjowanie obsługi administracyjnej aplikacji Zscaler Private Access (ZPA) w witrynie Azure portal.

    ![Token tworzenia prywatnego dostępu Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Aby zlokalizować **adres URL dzierżawy,** przejdź do **pozycji Konfiguracja > IdP .** Kliknij nazwę nowo dodanej konfiguracji IdP wymienioną na stronie.

    ![Nazwa Idp dostępu prywatnego Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Przewiń w dół, aby wyświetlić **punkt końcowy dostawcy usług SCIM** na końcu strony. Skopiuj **punkt końcowy dostawcy usług SCIM**. Ta wartość zostanie wprowadzona w polu Adres URL dzierżawy na karcie Inicjowanie obsługi administracyjnej aplikacji Zscaler Private Access (ZPA) w witrynie Azure portal.

    ![Adres URL scjowego dostępu prywatnego (ZPA) Zscaler](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Dodaj Zscaler Private Access (ZPA) z galerii

Przed skonfigurowaniem programu Zscaler Private Access (ZPA) do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy dodać program Zscaler Private Access (ZPA) z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać program Zscaler Private Access (ZPA) z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **zscaler private access (ZPA)**, wybierz **Zscaler Private Access (ZPA)** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Zscaler Private Access (ZPA) na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej dla programu Zscaler Private Access (ZPA) 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Zscaler Private Access (ZPA) na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Można również włączyć logowanie jednokrotne oparte na SAML dla Zscaler Private Access (ZPA), postępując zgodnie z instrukcjami podanymi w [samouczku logowania jednokrotnego Zscaler Private Access (ZPA).](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej użytkownika, chociaż te dwie funkcje wzajemnie się uzupełniają.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym SCIM programu Zscaler Private Access, zapoznaj się [z tą tą .](https://www.zscaler.com/partners/microsoft)

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla programu Zscaler Private Access (ZPA) w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Zscaler Private Access (ZPA)**.

    ![Łącze Zscaler Private Access (ZPA) na liście Aplikacje](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** wprowadź wartość **punktu końcowego dostawcy usług SCIM** pobraną wcześniej w **adresie URL dzierżawy**. Wprowadź wartość **tokenu nośnika** pobraną wcześniej w **tokenie tajnym**. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z usługą Zscaler Private Access (ZPA). Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Zscaler Private Access (ZPA) ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z dostępem prywatnym (ZPA) w skali.**

    ![Mapowania użytkowników zscaler private access (ZPA)](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Zscaler Private Access (ZPA) w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania kont użytkowników w Zscaler Private Access (ZPA) dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika programu Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy usługi Azure Active Directory z dostępem prywatnym (ZPA) w skali.**

    ![Mapowania grupowe zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do Zscaler Private Access (ZPA) w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **właściwości dopasowania** są używane do dopasowania grup w Zscaler Private Access (ZPA) dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy dostępu prywatnego Zscaler (ZPA)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla programu Zscaler Private Access (ZPA), zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Zscaler Private Access (ZPA), wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

15. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i śledzić łącza do raportu aktywności inicjowania obsługi administracyjnej, w którym opisano wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w usłudze Zscaler Private Access (ZPA).

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)

