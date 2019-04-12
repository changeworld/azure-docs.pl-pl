---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Evernote | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 2ce05e904484a6d773a0132734208b87e161f960
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499924"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Evernote

Z tego samouczka dowiesz się, jak można zintegrować aplikację Evernote z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Evernote z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować za pomocą usługi Azure AD, kto ma dostęp do aplikacji Evernote.
* Umożliwianie użytkownikom automatycznego logowania się do aplikacji Evernote (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Evernote, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Evernote z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Evernote obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** oraz **dostawcę tożsamości**

## <a name="adding-evernote-from-the-gallery"></a>Dodawanie aplikacji Evernote z galerii

Aby skonfigurować integrację aplikacji Evernote z usługą Azure AD, należy dodać aplikację Evernote z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Evernote z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Evernote**, wybierz opcję **Evernote** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Pozycja Evernote na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz funkcję logowania jednokrotnego usługi Azure AD z aplikacją Evernote, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Evernote.

Aby skonfigurować i przetestować funkcję logowania jednokrotnego usługi Azure AD z aplikacją Evernote, należy utworzyć następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Evernote](#configure-evernote-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Evernote](#create-evernote-test-user)** — aby w aplikacji Evernote znajdował się odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Evernote, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Evernote** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja języka SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL aplikacji Evernote na potrzeby logowania jednokrotnego](common/idp-identifier.png)

    W polu tekstowym **Identyfikator** wpisz adres URL: `https://www.evernote.com/saml2`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![image](common/both-preintegrated-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://www.evernote.com/Login.action`

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. Aby zmodyfikować **podpisywanie** opcje, kliknij przycisk **Edytuj** przycisk, aby otworzyć **certyfikat podpisywania SAML** okna dialogowego.

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Wybierz **odpowiedź i potwierdzenie SAML logowania** opcja dla **opcja podpisywania**.

    b. Kliknij pozycję **Zapisz**

8. W sekcji **Konfigurowanie aplikacji Evernote** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-evernote-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Evernote

1. W innym oknie przeglądarki internetowej zaloguj się do swojej firmowej witryny aplikacji Evernote jako administrator.

2. Przejdź do pozycji **Admin Console** (Konsola administracyjna)

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Security** (Zabezpieczenia) i wybierz pozycję **Single Sign-On** (Logowanie jednokrotne)

    ![Ustawienia logowania jednokrotnego](./media/evernote-tutorial/tutorial_evernote_sso.png)

4. Skonfiguruj następujące wartości:

    ![Ustawienia certyfikatu](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Włącz logowanie jednokrotne:** Logowanie jednokrotne jest domyślnie włączone (kliknij pozycję **Disable Single Sign-on** (Wyłącz logowanie jednokrotne), aby usunąć wymaganie logowania jednokrotnego)

    b. W polu tekstowym **SAML HTTP Request URL** (Adres URL żądań HTTP SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. Otwórz w Notatniku certyfikat pobrany z usługi Azure AD, skopiuj zawartość włącznie z pozycjami „BEGIN CERTIFICATE” i „END CERTIFICATE”, a następnie wklej ją w polu tekstowym **X.509 Certificate** (Certyfikat X.509). 

    d. Kliknij pozycję **Save Changes** (Zapisz zmiany)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Evernote.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Evernote**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Evernote**.

    ![Link do aplikacji Evernote na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-evernote-test-user"></a>Tworzenie użytkownika testowego aplikacji Evernote

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Evernote, należy ich aprowizować w aplikacji Evernote.  
W aplikacji Evernote aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do swojej firmowej witryny aplikacji Evernote jako administrator.

2. Kliknij pozycję **Admin Console** (Konsola administracyjna).

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Add users** (Dodaj użytkowników).

    ![Dodawanie użytkownika testowego](./media/evernote-tutorial/create_aaduser_0001.png)

4. W obszarze **Add team members** (Dodaj członków zespołu) w polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika i kliknij pozycję **Invite** (Zaproś).

    ![Dodawanie użytkownika testowego](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Po wysłaniu zaproszenia właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z prośbą o zaakceptowanie zaproszenia.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Evernote na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Evernote, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

