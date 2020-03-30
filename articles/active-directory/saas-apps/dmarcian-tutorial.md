---
title: 'Samouczek: Integracja usługi Azure Active Directory z dmarcian | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68823696"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Samouczek: Integracja dmarcian z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować dmarcian z usługi Azure Active Directory (Azure AD). Po zintegrowaniu dmarcian z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do dmarcian.
* Włącz użytkownikom automatyczne logowanie do dmarcian za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* dmarcian rejestracji jednokrotnej (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja dmarcian obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**

## <a name="adding-dmarcian-from-the-gallery"></a>Dodawanie aplikacji dmarcian z galerii

Aby skonfigurować integrację aplikacji dmarcian z usługą Azure AD, musisz dodać aplikację dmarcian z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **dmarcian** w polu wyszukiwania.
1. Wybierz **dmarcian** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO z dmarcian przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sygnowania, aby działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w dmarcian.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą dmarcian, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj dmarcian SSO](#configure-dmarcian-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego dmarcian](#create-dmarcian-test-user)** — mieć odpowiednik B.Simon w dmarcian, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **dmarcian** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** Jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizujesz te wartości, używając rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, co zostało opisane w dalszej części tego samouczka.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Konfigurowanie dmarcian SSO

1. Aby zautomatyzować konfigurację w dmarcian, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator dmarcian** skieruje Cię do aplikacji dmarcian. Stamtąd podaj poświadczenia administratora, aby zalogować się do dmarcian. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować dmarcian, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy dmarcian jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **Profile** (Profil) w prawym górnym rogu, a następnie przejdź do obszaru **Preferences** (Preferencje).

    ![Preferencje](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Przewiń w dół i kliknij sekcję **Single Sign-On** (Logowanie jednokrotne), a następnie kliknij przycisk **Configure** (Konfiguruj).

    ![Logowanie jednokrotne](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na stronie **SAML Single Sign-On** (Logowanie jednokrotne języka SAML) ustaw pozycję **Status** (Stan) na **Enabled** (Włączono) i wykonaj następujące kroki:

    ![Uwierzytelnianie](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **adres URL usługi Assertion Consumer Service** dla wystąpienia i wkleić go w polu tekstowym **Adres URL odpowiedzi** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **identyfikator jednostki** dla wystąpienia i wkleić go w polu tekstowym **Identyfikator** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Identity Provider Metadata** (Metadane dostawcy tożsamości) wklej **adres URL metadanych federacyjnych aplikacji** skopiowany z witryny Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Attribute Statements** (Instrukcje atrybutów) wklej adres URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * W sekcji **Set up Login URL** (Konfigurowanie adresu URL logowania) skopiuj **adres URL logowania** dla wystąpienia i wklej go w polu tekstowym **Adres URL logowania** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

        > [!Note]
        > Możesz zmodyfikować **adres URL logowania** zgodnie z zasadami Twojej organizacji.

    * Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do dmarcian.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **dmarcian**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-dmarcian-test-user"></a>Tworzenie użytkownika testowego aplikacji dmarcian

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji dmarcian, należy ich aprowizować w tej aplikacji. W aplikacji dmarcian aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do aplikacji dmarcian jako administrator zabezpieczeń.

2. Kliknij pozycję **Profile** (Profil) w górnym prawym rogu i przejdź do obszaru **Manage Users** (Zarządzanie użytkownikami).

    ![Użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Po prawej stronie sekcji **SSO Users** (Użytkownicy logowania jednokrotnego) kliknij pozycję **Add New User** (Dodaj nowego użytkownika).

    ![Dodawanie użytkownika](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. W wyskakującym okienku **Add New User** (Dodawanie nowego użytkownika) wykonaj następujące kroki:

    ![Nowy użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. W polu tekstowym **Nowy użytkownik e-mail** wprowadź adres e-mail użytkownika, taki jak **\@brittasimon contoso.com**.

    b. Jeśli chcesz nadać użytkownikowi uprawnienia administratora, zaznacz pole **Make User an Admin** (Oznacz użytkownika jako administratora).

    d. Kliknij **pozycję Dodaj użytkownika**.

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka dmarcian w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji dmarcian, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

