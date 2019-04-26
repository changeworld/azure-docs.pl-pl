---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Soloinsight-CloudGate SSO | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e8b2b4d1a660fe2f1289bba6fa596d08ec824b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60341474"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Soloinsight-CloudGate SSO

Z tego samouczka dowiesz się, jak zintegrować aplikację Soloinsight-CloudGate SSO z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Soloinsight-CloudGate SSO z usługą Azure AD oferuje następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Soloinsight-CloudGate SSO.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Soloinsight-CloudGate SSO (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Soloinsight-CloudGate SSO potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Soloinsight CloudGate SSO z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Soloinsight-CloudGate SSo obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługę**

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Dodawanie aplikacji Soloinsight-CloudGate SSO z galerii

Aby skonfigurować integrację aplikacji Soloinsight-CloudGate SSO z usługą Azure AD, należy dodać aplikację Soloinsight-CloudGate SSO z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Soloinsight-CloudGate SSO z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Soloinsight-CloudGate SSO**, wybierz pozycję **Soloinsight-CloudGate SSO** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Soloinsight-CloudGate SSO na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Soloinsight-CloudGate SSO, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Soloinsight-CloudGate SSO.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Soloinsight-CloudGate SSO, należy wykonać kroki opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** — aby Britta Simon miała swojego odpowiednika w aplikacji Soloinsight-CloudGate SSO połączonego z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Soloinsight-CloudGate SSO, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Soloinsight-CloudGate SSO** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje dotyczące domeny i adresów URL logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania, co zostało wyjaśnione poniżej w sekcji **Konfigurowanie logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO** samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Soloinsight-CloudGate SSO** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO

1. Aby uzyskać wartości do wklejenia w witrynie Azure Portal podczas konfigurowania podstawowej konfiguracji SAML, zaloguj się do internetowego portalu CloudGate przy użyciu poświadczeń, a następnie przejdź do ustawień logowania jednokrotnego, które można znaleźć w następującej ścieżce: **Strona główna > Administracja > Ustawienia systemu > Ogólne**.

    ![Ustawienia aplikacji CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **Adres URL użytkownika języka SAML**

    * Skonfiguruj linki dostępne w polach **Saml Consumer URL** (Adres URL użytkownika języka SAML) i **Redirect URL** (Adres URL przekierowania) i wklej je w witrynie Azure Portal, w sekcji **Podstawowa konfiguracja protokołu SAML** odpowiednio dla pól **Identyfikator (identyfikator jednostki)** i **Adres URL odpowiedzi**.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **Certyfikat podpisywania SAML**

    * Przejdź do źródła pliku certyfikatu (Base64), który został pobrany z list certyfikatów podpisywania SAML w witrynie Azure Portal i kliknij je prawym przyciskiem myszy. Wybierz opcję **Edit with Notepad++** (Edytuj za pomocą Notatnika++) z listy. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Skopiuj zawartość pliku Notatnika++ certyfikatu (Base64).

        ![Kopiowanie certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Wklej zawartość w obszarze ustawień logowania jednokrotnego portalu internetowego CloudGate w polu **Certificate** (Certyfikat), a następnie kliknij przycisk zapisywania.

        ![Portal certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Default Group** (Grupa domyślna)

    * Wybierz pozycję **Business Admin** (Administrator firmy) z listy rozwijanej **Default Group** (Grupa domyślna) w portalu internetowym CloudGate

        ![Grupa domyślna](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **Adres URL identyfikatora usługi AD i adres URL logowania**

    * Skopiowany **adres URL logowania** witryny Azure Portal z konfiguracji z sekcji **Konfigurowanie aplikacji Soloinsight-CloudGate SSO** wprowadź w sekcji ustawień logowania jednokrotnego w portalu internetowym CloudGate. 

    * Wklej link **adresu URL logowania** z witryny Azure Portal w polu **AD Login URL** (Adres URL logowania do usługi AD) w portalu internetowym CloudGate.
     
    * Wklej link **identyfikatora usługi Azure AD** z witryny Azure Portal w polu **AD Identifier** (Identyfikator usługi AD) w portalu internetowym CloudGate.

        ![Logowanie w usłudze AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Soloinsight-CloudGate SSO.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Soloinsight-CloudGate SSO**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Soloinsight-CloudGate SSO**.

    ![Link do aplikacji Soloinsight-CloudGate SSO na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Tworzenie użytkownika testowego aplikacji Soloinsight-CloudGate SSO

Aby utworzyć użytkownika testowego, wybierz pozycję **Employees** z menu głównego w internetowym portalu aplikacji CloudGate i wypełnij formularz Add new employee (Dodawanie nowego pracownika). Poziom urzędu do przypisania do użytkownika testowego to **Business Admin** (Administrator firmy). Kliknij raz pozycję **Create** (Utwórz) po wypełnieniu wszystkich wymaganych pól.

![Testowanie pracownika](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Soloinsight-CloudGate SSO w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Soloinsight-CloudGate SSO, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

