---
title: 'Samouczek: integracja usługi Azure Active Directory z usługą logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9994232259231014c37beae81a5de007b142c732
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163091"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Samouczek: integracja usługi Azure Active Directory z usługą logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH

Z tego samouczka dowiesz się, jak zintegrować usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do usługi logowania jednokrotnego SAML dla usługi Jira firmy resolution GmbH (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji z usługą Azure AD w usłudze logowania jednokrotnego SAML dla programu Jira firmy resolution GmbH potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** i **dostawcę tożsamości**

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Dodawanie usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z galerii

Aby skonfigurować integrację usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z usługą Azure AD, musisz dodać usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH**, wybierz pozycję **Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w usłudze logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usłudze logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH, musisz utworzyć następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** — aby mieć w usłudze logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usłudze logowania jednokrotnego SAML dla programu Jira firmy resolution GmbH, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje na temat domeny i adresów URL usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    d. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Informacje na temat domeny i adresów URL usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej dla klientów usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH](https://www.resolution.de/go/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Skonfiguruj usługę logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH

1. W innym oknie przeglądarki internetowej zaloguj się do swojego **portalu administracyjnego usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH** jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon1.png)

3. Nastąpi przekierowanie na stronę dostępu administratora. Wprowadź **hasło** i kliknij przycisk **Confirm** (Potwierdź).

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon2.png)

4. W sekcji karty Dodatki kliknij pozycję **Znajdź nowe dodatki**. Wyszukaj pozycję **Usługa logowania jednokrotnego (SSO) SAML dla aplikacji JIRA** i kliknij przycisk **Zainstaluj**, aby zainstalować nową wtyczkę SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon7.png)

5. Rozpocznie się instalacja wtyczki. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon8.png)

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon9.png)

6.  Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon10.png)
    
8. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon11.png)

9. Na stronie **SAML SingleSignOn Plugin Configuration** (Konfiguracja wtyczki logowania jednokrotnego protokołu SAML) kliknij przycisk **Add new IdP** (Dodaj nowego dostawcę tożsamości), aby skonfigurować ustawienia dostawcy tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon4.png)

10. Na stronie **Choose your SAML Identity Provider** (Wybieranie dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5a.png)
 
    a. W polu IdP type (Typ dostawcy tożsamości) wybierz pozycję **Azure AD**.
    
    b. W polu **Name** (Nazwa) wprowadź nazwę dostawcy tożsamości, np. Azure AD.
    
    d. W polu **Description** (Opis) wprowadź opis dostawcy tożsamości, np. Azure AD.
    
    d. Kliknij przycisk **Dalej**.
    
11. Na stronie **Identity provider configuration** (Konfiguracja dostawcy tożsamości) kliknij przycisk **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5b.png)

12. Na stronie **Import SAML IdP Metadata** (Importuj metadane dostawcy tożsamości protokołu SAML) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5c.png)

    a. Kliknij przycisk **Load File** (Załaduj plik), a następnie wybierz plik XML metadanych pobrany w kroku 5.

    b. Kliknij przycisk **Import** (Importuj).
    
    d. Zaczekaj chwilę na pomyślne zakończenie importowania.
    
    d. Kliknij przycisk **Next** (Dalej).
    
13. Na stronie **User ID attribute and transformation** (Atrybut identyfikatora użytkownika i transformacja) kliknij przycisk **Next** (Dalej).

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5d.png)
    
14. Na stronie **User creation and update** (Tworzenie i aktualizowanie użytkownika) kliknij pozycję **Save & Next** (Zapisz i przejdź dalej), aby zapisać ustawienia.   
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6a.png)
    
15. Na stronie **Test your settings** (Przetestuj ustawienia) kliknij pozycję **Skip test & configure manually** (Pomiń test i skonfiguruj ręcznie), aby pominąć na razie test użytkownika. Zostanie on wykonany w następnej sekcji i będzie wymagał wprowadzenia pewnych ustawień w witrynie Azure Portal. 
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6b.png)
    
16. W wyświetlonym oknie dialogowym o treści **Skipping the test means...** (Pominięcie testu oznacza...) kliknij przycisk **OK**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie **Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz pozycję **Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH**.

    ![Usługa logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Tworzenie użytkownika testowego usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH

Aby umożliwić użytkownikom usługi Azure AD logowanie się do usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH, należy aprowizować ich w tej usłudze.  
W przypadku usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH aprowizację wykonuje się ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user1.png) 

3. Nastąpi przekierowanie do strony dostępu administratora. Podaj wartość w polu **Password** (Hasło) i kliknij przycisk **Confirm** (Potwierdź).

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user2.png) 

4. W sekcji karty **User management** (Zarządzanie użytkownikami) kliknij pozycję **create user** (utwórz użytkownika).

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user3.png) 

5. Na stronie okna dialogowego **„Create new user”** (Tworzenie nowego użytkownika) wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user4.png) 

    a. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    d. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. Kliknij pozycję **Create user** (Utwórz użytkownika).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH w panelu dostępu powinno nastąpić automatyczne zalogowanie się do usługi logowania jednokrotnego SAML dla aplikacji Jira firmy resolution GmbH, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

