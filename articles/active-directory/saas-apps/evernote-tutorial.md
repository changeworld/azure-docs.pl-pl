---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z evernote | Dokumenty firmy Microsoft'
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
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121624"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z evernote

W tym samouczku dowiesz się, jak zintegrować Evernote z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Evernote z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Evernote.
* Włącz użytkownikom automatyczne logowanie się do Evernote za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego (SSO) evernote.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja Evernote obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-evernote-from-the-gallery"></a>Dodawanie aplikacji Evernote z galerii

Aby skonfigurować integrację aplikacji Evernote z usługą Azure AD, należy dodać aplikację Evernote z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Evernote** w polu wyszukiwania.
1. Wybierz **evernote** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla evernote

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą evernote przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Evernote.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą evernote, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj dodatek SSO Evernote](#configure-evernote-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Evernote](#create-evernote-test-user)** — aby mieć odpowiednik B.Simon w Evernote, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Evernote** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    W polu tekstowym **Identyfikator** wpisz adres URL: `https://www.evernote.com/saml2`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://www.evernote.com/Login.action`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. Aby zmodyfikować opcje **podpisywania,** kliknij przycisk **Edytuj,** aby otworzyć okno dialogowe **Saml Podpisywanie certyfikatu.**

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Wybierz opcję **Podpisz odpowiedź SAML i asercja** dla **opcji Podpisywanie**.

    b. Kliknij **przycisk Zapisz**

1. W sekcji **Konfigurowanie Evernote** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Evernote.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Evernote**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-evernote-sso"></a>Konfigurowanie sytcha w programie Evernote

1. Aby zautomatyzować konfigurację w evernote, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **na konfigurację Evernote** przekieruje Cię do aplikacji Evernote. Stamtąd podaj poświadczenia administratora, aby zalogować się do Evernote. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować evernote ręcznie, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Evernote jako administrator i wykonaj następujące czynności:

4. Przejdź do pozycji **Admin Console** (Konsola administracyjna)

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Security** (Zabezpieczenia) i wybierz pozycję **Single Sign-On** (Logowanie jednokrotne)

    ![Ustawienia logowania jednokrotnego](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Skonfiguruj następujące wartości:

    ![Ustawienia certyfikatu](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Włącz opcję SSO:** Logowanie jednokrotne jest domyślnie włączone (kliknij przycisk **Wyłącz logowanie jednokrotne,** aby usunąć wymaganie logowania jednokrotnego)

    b. W polu tekstowym **SAML HTTP Request URL** (Adres URL żądań HTTP SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. Otwórz w Notatniku certyfikat pobrany z usługi Azure AD, skopiuj zawartość włącznie z pozycjami „BEGIN CERTIFICATE” i „END CERTIFICATE”, a następnie wklej ją w polu tekstowym **X.509 Certificate** (Certyfikat X.509). 

    d. Kliknij pozycję **Save Changes** (Zapisz zmiany)

### <a name="create-evernote-test-user"></a>Tworzenie użytkownika testowego aplikacji Evernote

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Evernote, muszą one być aprowidywne w Evernote.  
W aplikacji Evernote aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Evernote jako administrator.

2. Kliknij pozycję **Admin Console** (Konsola administracyjna).

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Add users** (Dodaj użytkowników).

    ![Dodawanie użytkownika testowego](./media/evernote-tutorial/create_aaduser_0001.png)

4. W obszarze **Add team members** (Dodaj członków zespołu) w polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika i kliknij pozycję **Invite** (Zaproś).

    ![Dodawanie użytkownika testowego](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Po wysłaniu zaproszenia właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z prośbą o zaakceptowanie zaproszenia.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Evernote na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Evernote, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj evernote z usługą Azure AD](https://aad.portal.azure.com/)

