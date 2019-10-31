---
title: 'Samouczek: integracja Azure Active Directory z usługą GitHub | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5021d3a8bed48003f38f01c3d61eac4bdd96b0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159208"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Samouczek: integracja Azure Active Directory z usługą GitHub

Z tego samouczka dowiesz się, jak zintegrować aplikację GitHub z usługą Azure Active Directory (Azure AD).
Integracja aplikacji GitHub z usługą Azure AD oferuje następujące korzyści:

* Z poziomu usługi Azure AD możesz kontrolować, kto ma dostęp do aplikacji GitHub.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji GitHub (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją GitHub, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Organizacja usługi GitHub utworzona w [chmurze usługi GitHub dla przedsiębiorstw](https://help.github.com/articles/github-s-products/#github-enterprise), która wymaga [planu rozliczeniowego usługi GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja GitHub obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi (SP)**

* Aplikacja GitHub obsługuje [**zautomatyzowaną** aprowizację użytkowników](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Dodawanie aplikacji GitHub z galerii

Aby skonfigurować integrację aplikacji GitHub z usługą Azure AD, należy dodać z galerii usługę GitHub do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację GitHub z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **GitHub**, wybierz pozycję **GitHub.com** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Aplikacja GitHub na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w aplikacji GitHub w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji GitHub.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji GitHub, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji GitHub](#configure-github-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji GitHub](#create-github-test-user)** — aby mieć w aplikacji GitHub odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji GitHub, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **GitHub** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL pojedynczego logowania aplikacji GitHub](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://github.com/orgs/<entity-id>/sso`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Należy pamiętać, że nie są to rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W tym miejscu zalecamy użycie unikatowej wartości ciągu w identyfikatorze. Przejdź do sekcji dotyczącej administrowania aplikacją GitHub, aby pobrać te wartości.

5. Aplikacja GitHub oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja GitHub oczekuje, że atrybut **nameidentifier** będzie mapowany na atrybut **user.mail**, dlatego należy zmodyfikować mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutów.

    ![image](common/edit-attribute.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie aplikacji GitHub** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-github-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji GitHub

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji GitHub jako administrator.

2. Przejdź do karty **Settings** (Ustawienia) i kliknij pozycję **Security** (Zabezpieczenia)

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Zaznacz pole **Enable SAML authentication** (Włącz uwierzytelnianie SAML), aby aktywować pola konfiguracji logowania jednokrotnego. Następnie użyj wartości adresu URL logowania jednokrotnego do zaktualizowania wartości adresu URL logowania jednokrotnego w konfiguracji usługi Azure AD.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Skonfiguruj następujące pola:

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. W polu tekstowym **Sign on URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Issuer** (Wystawca) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. Otwórz certyfikat pobrany z witryny Azure Portal w programie Notatnik i wklej jego zawartość w polu tekstowym **Public Certificate** (Certyfikat publiczny).

    d. Kliknij ikonę **edycji**, aby zmodyfikować wartości pól **Signature Method** (Metoda podpisu) i **Digest Method** (Metoda szyfrowania) z **RSA-SHA1** i **SHA1** na **RSA-SHA256** i **SHA256**, jak pokazano poniżej.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Kliknij przycisk **Test SAML configuration** (Testuj konfigurację SAML), aby się upewnić, że podczas logowania jednokrotnego nie występują żadne niepowodzenia weryfikacji ani błędy.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Kliknij pozycję **Zapisz**

> [!NOTE]
> Logowanie jednokrotne w aplikacji GitHub przeprowadza uwierzytelnianie w konkretnej organizacji w usłudze GitHub i nie zastępuje uwierzytelniania samej usługi GitHub. W związku z tym jeśli sesja użytkownika w witrynie github.com wygaśnie, w czasie logowania jednokrotnego może pojawić się prośba o uwierzytelnienie za pomocą identyfikatora/hasła usługi GitHub.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji GitHub.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **GitHub**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **GitHub**.

    ![Link do aplikacji GitHub na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-github-test-user"></a>Tworzenie użytkownika testowego w aplikacji GitHub

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w aplikacji GitHub. Aplikacja GitHub obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](github-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji GitHub jako administrator.

2. Kliknij kartę **People** (Osoby).

    ![Osób](./media/github-tutorial/tutorial_github_config_github_08.png "Ludzie")

3. Kliknij przycisk **Invite member** (Zaproś członka).

    ![Zapraszanie użytkowników](./media/github-tutorial/tutorial_github_config_github_09.png "Zapraszanie użytkowników")

4. W oknie dialogowym **Invite member** (Zapraszanie członka) wykonaj następujące kroki:

    a. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika Britta Simon.

    ![Zapraszanie osób](./media/github-tutorial/tutorial_github_config_github_10.png "Zapraszanie osób")

    b. Kliknij przycisk **Send Invitation** (Wyślij zaproszenie).

    ![Zapraszanie osób](./media/github-tutorial/tutorial_github_config_github_11.png "Zapraszanie osób")

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka GitHub na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji GitHub, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
