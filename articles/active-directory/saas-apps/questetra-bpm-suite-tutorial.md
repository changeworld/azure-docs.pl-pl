---
title: 'Samouczek: Integracja usługi Azure Active Directory z pakietem Questetra BPM Suite | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pakietem Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f58d6cbc6ec04e51e105662dff31c60ff502584c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093360"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem Questetra BPM Suite

W tym samouczku dowiesz się, jak zintegrować pakiet Questetra BPM Suite z usługą Azure Active Directory (Azure AD).
Integracja pakietu Questetra BPM Suite z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do pakietu Questetra BPM Suite.
* Możesz włączyć automatyczne logowanie użytkowników do pakietu Questetra BPM Suite (logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem Questetra BPM Suite, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Questetra BPM Suite

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Questetra BPM Suite obsługuje zainicjowane przez **SP** SSO

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Dodawanie pakietu Questetra BPM Suite z galerii

Aby skonfigurować integrację pakietu Questetra BPM Suite z usługą Azure AD, należy dodać pakiet Questetra BPM Suite z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet Questetra BPM Suite z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Questetra BPM Suite**, wybierz **Questetra BPM Suite** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Questetra BPM Suite na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Questetra BPM Suite na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w pakiecie Questetra BPM Suite.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą pakietu Questetra BPM Suite, należy wykonać następujące elementy konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Questetra BPM Suite](#configure-questetra-bpm-suite-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Questetra BPM Suite](#create-questetra-bpm-suite-test-user)** — aby mieć odpowiednik Britta Simon w pakiecie Questetra BPM Suite, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą pakietu Questetra BPM Suite, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Questetra BPM Suite** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL Questetra BPM Suite](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Możesz uzyskać te wartości z sekcji **INFORMACJE SP** na stronie firmy **Questetra BPM Suite,** która została wyjaśniona w dalszej części samouczka lub skontaktuj się z [zespołem pomocy technicznej klienta Questetra BPM Suite.](https://www.questetra.com/contact/) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie pakietu Questetra BPM Suite** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurowanie logowania jednokrotnego pakietu Questetra BPM Suite

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy **Questetra BPM Suite** jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia systemowe**. 
   
    ![Logowanie jednokrotne usługi Azure AD][10]

3. Aby otworzyć stronę **SingleSignOnSAML,** kliknij przycisk **SSO (SAML)**. 
   
    ![Logowanie jednokrotne usługi Azure AD][11]

4. Na stronie firmy **Questetra BPM Suite** w sekcji **Informacje o SP** wykonaj następujące czynności:

    a. Skopiuj **adres URL usługi ACS,** a następnie wklej go do pola tekstowego **Podpisz adres URL** w sekcji **Podstawowa konfiguracja SAML** z witryny Azure portal.
    
    b. Skopiuj **identyfikator jednostki,** a następnie wklej go do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** z witryny Azure portal.

5. Na stronie firmy **Questetra BPM Suite** wykonaj następujące czynności: 
   
    ![Konfigurowanie logowania jednokrotnego][15]
   
    a. Wybierz **włącz logowanie jednokrotne**.
   
    b. W polu tekstowym **identyfikatora encji** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure portal.
    
    d. W polu tekstowym **adresu URL strony logowania wklej** wartość adresu URL **logowania** skopiowanego z witryny Azure portal.
    
    d. W polu **tekstowym adresu URL strony wylogowywania** wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure Portal.
    
    e. W obszarze tekstowym **Format** `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`nazwy wpisz polecenie .

    f. Otwórz zakodowany certyfikat **Base-64** w notatniku pobranym z witryny Azure Portal, skopiuj jego zawartość do schowka, a następnie wklej ją do pola tekstowego **certyfikatu sprawdzania poprawności.** 

    g. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do Questetra BPM Suite.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pakiet **Questetra BPM Suite**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Questetra BPM Suite**.

    ![Link Questetra BPM Suite na liście Aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-questetra-bpm-suite-test-user"></a>Utwórz użytkownika testowego Questetra BPM Suite

Celem tej sekcji jest stworzenie użytkownika o nazwie Britta Simon w Questetra BPM Suite.

**Aby utworzyć użytkownika o nazwie Britta Simon w apartamencie Questetra BPM Suite, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Questetra BPM Suite jako administrator.

2. Przejdź do **ustawień systemowych > listy użytkowników > nowego użytkownika**.
 
3. W oknie dialogowym Nowy użytkownik wykonaj następujące czynności: 
   
    ![Tworzenie użytkownika testowego][300] 
   
    a. W polach tekstowych **Nazwa** wpisz britta.simon@contoso.com **nazwę** użytkownika .
   
    b. W **polach tekstowych Poczta e-mail** wpisz **wiadomość e-mail** użytkownika britta.simon@contoso.com.
   
    d. W **polach tekstowych Hasło** wpisz **hasło** użytkownika.
    
    d. Kliknij **pozycję Dodaj nowego użytkownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Questetra BPM Suite w Panelu dostępu należy automatycznie zalogować się do pakietu Questetra BPM Suite, dla którego skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png