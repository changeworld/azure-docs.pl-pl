---
title: 'Samouczek: Integracja usługi Azure Active Directory z samol SSO dla Jira przez Resolution GmbH | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160133"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Samouczek: Integracja usługi Azure Active Directory z samol SSO dla Jira przez rozdzielczość GmbH

W tym samouczku dowiesz się, jak skonfigurować samol SSO dla Jira przez resolution GmbH z usługi Azure Active Directory (Azure AD).
Zintegrowanie usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto może logować się do Jira za pomocą wtyczki SAML SSO przez resolution GmbH.
* Można włączyć użytkowników, aby automatycznie zalogować się do Jira z ich kont usługi Azure AD przy użyciu SAML SSO dla Jira przez resolution GmbH (logowanie jednokrotne).
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD i saml SSO dla Jira przez resolution GmbH, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SAML SSO dla Jira w rozdzielczości GmbH obsługuje **SP** i **IDP** zainicjowane SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Dodawanie aplikacji przedsiębiorstwa do logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne w usłudze Azure AD, należy dodać nową aplikację dla przedsiębiorstwa. W galerii, istnieje wstępnie skonfigurowana aplikacja preset do tego, **SAML SSO dla Jira przez rozdzielczość GmbH**.

**Aby dodać usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **pozycji Aplikacje przedsiębiorstwa**, a następnie kliknij pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAML SSO dla Jira przez resolution GmbH**, wybierz **SAML SSO dla Jira przez rozdzielczość GmbH** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację. Można również zmienić nazwę aplikacji dla przedsiębiorstw.

     ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfigurowanie i testowanie logowania jednokrotnego za pomocą wtyczki SAML SSO i usługi Azure AD

W tej sekcji będzie testować i konfigurować logowanie jednokrotne do Jira dla użytkownika usługi Azure AD. Zostanie to zrobione dla użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.

Aby skonfigurować i przetestować logowanie jednokrotne, należy wykonać następujące kroki:

1. **[Konfigurowanie aplikacji przedsiębiorstwa usługi Azure AD dla logowania jednokrotnego](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** — konfigurowanie aplikacji przedsiębiorstwa usługi Azure AD dla logowania jednokrotnego
2. **[Skonfiguruj wtyczkę SAML SSO swojego wystąpienia Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)** - Skonfiguruj ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — tworzenie użytkownika testowego w usłudze Azure AD.
1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — włączanie użytkownika testowego do korzystania z logowania jednokrotnego po stronie platformy Azure.
1. **[Tworzenie użytkownika testowego w Jira](#create-the-test-user-also-in-jira)** — tworzenie użytkownika testowego odpowiednika w Jira dla użytkownika testowego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — sprawdź, czy konfiguracja działa.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurowanie aplikacji przedsiębiorstwa usługi Azure AD do logowania jednokrotnego

W tej sekcji skonfigurować logowanie jednokrotne w witrynie Azure portal.

Aby skonfigurować logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla Jira przez resolution GmbH, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/), w właśnie **utworzonym samouczce SAML SSO dla Jira przez rozwiązanie GmbH** enterprise aplikacji, wybierz **logowanie jednokrotne** w lewym panelu.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W obszarze **Wybierz metodę logowania jednokrotnego**wybierz tryb **SAML,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Następnie kliknij ikonę **Edytuj,** aby otworzyć podstawowe okno dialogowe **Konfiguracja SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **IDP,** w sekcji **Podstawowa konfiguracja SAML,** wykonaj następujące czynności:

    ![Informacje na temat domeny i adresów URL usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    d. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym za pomocą **dodatku SP:**

    ![Informacje na temat domeny i adresów URL usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > W przypadku identyfikatora, adresu URL odpowiedzi ** \<** i adresu URL logowania, zastąp adres url podstawowy serwera>z podstawowym adresem URL wystąpienia Jira. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. Jeśli masz problem, skontaktuj się z nami pod adresem [SAML SSO for Jira by resolution GmbH Client support team](https://www.resolution.de/go/support).

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** pobierz **kod XML metadanych federacji** i zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurowanie wtyczki SAML SSO wystąpienia Jira 

1. W innym oknie przeglądarki internetowej zaloguj się do wystąpienia Jira jako administrator.

2. Umieść wskaźnik myszy na trybiku po prawej stronie i kliknij pozycję **Zarządzaj aplikacjami**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon1.png)

3. Jeśli zostaniesz przekierowany do strony Dostęp administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź.**

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon2.png)

4. Jira zwykle przekierowuje cię na rynek Atlassian. Jeśli nie, kliknij **znajdź nowe aplikacje** w lewym panelu. Wyszukaj **saml single sign on (SSO) dla JIRA** i kliknij przycisk **Zainstaluj,** aby zainstalować wtyczkę SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store.png)

5. Rozpocznie się instalacja wtyczki. Po zakończeniu kliknij przycisk **Zamknij.**

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-2.png)

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-3.png)

6. Następnie kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-4.png)
    
8. Następnie kliknij przycisk **Konfiguruj,** aby skonfigurować właśnie zainstalowaną wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/store-5.png)

9. W **Kreatorze konfiguracji wtyczki SAML SingleSignOn** kliknij pozycję **Dodaj nowy dostawca tożsamości,** aby skonfigurować usługę Azure AD jako nowego dostawcy tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon4.png) 

10. Na stronie **Wybierz dostawcę tożsamości SAML** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5a.png)
 
    a. W polu IdP type (Typ dostawcy tożsamości) wybierz pozycję **Azure AD**.
    
    b. Dodaj **nazwę** dostawcy tożsamości (np. usługi Azure AD).
    
    d. Dodaj (opcjonalny) **opis** dostawcy tożsamości (np. usługi Azure AD).
    
    d. Kliknij przycisk **alej**.
    
11. Na stronie **Konfiguracji dostawcy tożsamości** kliknij przycisk **Dalej**.
 
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5b.png)

12. Na stronie **Import SAML IdP Metadata** (Importuj metadane dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5c.png)

    a. Kliknij przycisk **Wybierz plik XML metadanych** i wybierz pobrany wcześniej plik **XML metadanych federacji.**

    b. Kliknij przycisk **Importuj.**
     
    d. Poczekaj krótko, aż importowanie zakończy się pomyślnie.  
     
    d. Kliknij przycisk **Dalej**.
    
13. Na stronie **atrybutu i transformacji identyfikator użytkownika** kliknij przycisk **Dalej.**

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5d.png)
    
14. Na stronie **Tworzenie i aktualizowanie użytkownika** kliknij pozycję Zapisz & **Dalej,** aby zapisać ustawienia.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6a.png)
    
15. Na stronie **Testowanie ustawień** kliknij pozycję **Pomiń test, & skonfigurować ręcznie,** aby pominąć test użytkownika. Zostanie to wykonane w następnej sekcji i wymaga pewnych ustawień w witrynie Azure portal.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6b.png)
    
16. Kliknij **przycisk OK,** aby pominąć ostrzeżenie.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon. Z użytkownikiem, można przetestować logowanie jednokrotne.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We **właściwościach Użytkownika**wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź pole **Britta Simon**.
  
    b. W polu **Nazwa** użytkownika <b>BrittaSimon@contoso.com</b>wprowadź .

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji dodasz Britta Simon do aplikacji dla przedsiębiorstwa, która umożliwia jej korzystanie z logowania jednokrotnego.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**. 

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wyszukaj aplikację dla przedsiębiorstwa utworzoną na początku tego samouczka. Jeśli po wykonaniu kroków samouczka, to się nazywa **SAML SSO dla Jira przez rozdzielczość GmbH**. Jeśli nadano mu inną nazwę, wyszukaj tę nazwę.

    ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście aplikacji](common/all-applications.png)

3. W lewym panelu kliknij pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-the-test-user-also-in-jira"></a>Tworzenie użytkownika testowego również w Jira

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do saml SSO dla Jira przez resolution GmbH, muszą one być aprowidywne do SAML SSO dla Jira przez resolution GmbH. W przypadku tego samouczka, trzeba wykonać inicjowania obsługi administracyjnej ręcznie. Istnieją jednak również inne modele inicjowania obsługi administracyjnej dostępne dla wtyczki SSO SAML przez rozdzielczość, na przykład **just in time** inicjowania obsługi administracyjnej. Zapoznaj się z ich dokumentacją w [SAML SSO w drodze resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Jeśli masz pytanie na ten temat, skontaktuj się z pomocą techniczną w [pomocy technicznej rozdzielczości](https://www.resolution.de/go/support).

**Aby ręcznie aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do instancji Jira jako administrator.

2. Umieść wskaźnik myszy na trybiku i wybierz **pozycję Zarządzanie użytkownikami**.

   ![Dodawanie pracownika](./media/samlssojira-tutorial/user1.png)

3. Jeśli zostaniesz przekierowany do strony Dostęp administratora, wprowadź **hasło** i kliknij przycisk **Potwierdź.**

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user2.png) 

4. W sekcji **Karta Zarządzanie użytkownikami** kliknij pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user3-new.png) 

5. Na stronie okna dialogowego **"Tworzenie nowego użytkownika"** wykonaj następujące czynności. Musisz utworzyć użytkownika dokładnie tak, jak w usłudze Azure AD:

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user4-new.png) 

    a. W **polach tekstowych Adres e-mail** wpisz <b>BrittaSimon@contoso.com</b>adres e-mail użytkownika: .

    b. W tekście **Imię i nazwisko** wpisz pełną nazwę użytkownika: **Britta Simon**.

    d. W polach **tekstowych Nazwa użytkownika** wpisz <b>BrittaSimon@contoso.com</b>adres e-mail użytkownika: . 

    d. W **polach tekstowych Hasło** wprowadź hasło użytkownika.

    e. Kliknij **przycisk Utwórz użytkownika,** aby zakończyć tworzenie użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH w panelu dostępu powinno nastąpić automatyczne zalogowanie się do usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Można również przetestować logowanie jednokrotne, jeśli przejdziesz do [https://\<server-base-url>/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Zastąp ** \<>adres url serwera** bazowego za pomocą podstawowego adresu URL wystąpienia Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Włączanie przekierowania logowania jednokrotnego dla Jira

Jak wspomniano w sekcji przed, obecnie istnieją dwa sposoby wyzwalania logowania jednokrotnego. Korzystając z **witryny Azure portal** lub używając **specjalnego łącza do wystąpienia Jira.** Wtyczka SAML SSO przez rozdzielczość GmbH pozwala również na wyzwalanie logowania jednokrotnego, po prostu **uzyskując dostęp do dowolnego adresu URL wskazującego na wystąpienie Jira.**

W istocie, wszyscy użytkownicy uzyskujący dostęp do Jira zostaną przekierowani do logowania jednokrotnego po aktywacji opcji w wtyczce.

Aby aktywować przekierowanie SSO, wykonaj następujące czynności w **wystąpieniu Jira:**

1. Przejdź do strony konfiguracji wtyczki SAML SSO w Jira.
1. Kliknij **przekierowanie** w lewym panelu.
![](./media/samlssojira-tutorial/ssore1.png)

1. Zaznacz **opcję Przekierowuj SSO**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Naciśnij przycisk **Zapisz ustawienia** w prawym górnym rogu.

Po aktywacji opcji, nadal można dotrzeć do monitu o nazwę użytkownika / hasło, jeśli **włącz nosso** opcja jest zaznaczona przez przejście do [https://\<server-base-url>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Jak zawsze, zastąp ** \<adres url serwera-base>** z podstawowym adresem URL.


## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

