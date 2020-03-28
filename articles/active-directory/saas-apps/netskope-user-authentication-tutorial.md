---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z uwierzytelnianiem użytkownika netskope | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a uwierzytelnianiem użytkowników netskope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9bb71cd-aaf9-4403-aca5-c5a349ec562a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33af932e405552cf9d8f5aaf6d42cbd095607b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74085373"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z uwierzytelnianiem użytkownika netskope

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie użytkowników netskope z usługą Azure Active Directory (Azure AD). Po zintegrowaniu uwierzytelniania użytkownika netskope z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do uwierzytelniania użytkownika Netskope.
* Włącz użytkownikom automatyczne logowanie się do uwierzytelniania użytkowników netskope za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego netskope user authentication (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Uwierzytelnianie użytkownika Netskope obsługuje jednostkę SSO inicjowane przez **sp i idp**

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Dodawanie uwierzytelniania użytkownika Netskope z galerii

Aby skonfigurować integrację uwierzytelniania użytkowników Netskope z usługą Azure AD, należy dodać uwierzytelnianie użytkownika netskope z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Netskope User Authentication** w polu wyszukiwania.
1. Wybierz **pozycję Uwierzytelnianie użytkownika Netskope** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD w celu uwierzytelniania użytkownika usługi Netskope

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą uwierzytelniania użytkownika Netskope przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w uwierzytelnianiu użytkownika netskope.

Aby skonfigurować i przetestować usługę Azure AD SSO przy użyciu uwierzytelniania użytkownika netskope, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne uwierzytelnienia użytkownika Netskope](#configure-netskope-user-authentication-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Netskope User Authentication](#create-netskope-user-authentication-test-user)** - aby mieć odpowiednik B.Simon w Netskope User Authentication, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Netskope User Authentication** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<tenantname>.goskope.com/<customer entered string>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Otrzymasz te wartości wyjaśnione w dalszej części samouczka.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Wartości adresu URL logowania nie są prawdziwe. Zaktualizuj wartość adresu URL logowania za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta uwierzytelniania użytkowników Netskope,](mailto:support@netskope.com) aby uzyskać wartość adresu URL logowania. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie uwierzytelniania użytkowników Netskope** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do netskope uwierzytelniania użytkownika.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Uwierzytelnianie użytkownika Netskope**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurowanie identyfikatora szyku uwierzytelniania użytkownika netskope

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy Netskope User Authentication jako administrator.

1. Kliknij kartę **Aktywna platforma.**

    ![Konfiguracja uwierzytelniania użytkowników Netskope](./media/netskope-user-authentication-tutorial/user1.png)

1. Przewiń w dół do **serwera proxy do przodu** i wybierz **SAML**.

    ![Konfiguracja uwierzytelniania użytkowników Netskope](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Na stronie **Ustawienia SAML** wykonaj następujące czynności:

    ![Konfiguracja uwierzytelniania użytkowników Netskope](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Skopiuj wartość **identyfikatora jednostki SAML** i wklej ją do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w portalu Azure.

    b. Skopiuj wartość **adresu URL SAML ACS** i wklej ją do pola tekstowego **Odpowiedz adres URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

1. Kliknij **pozycję DODAJ KONTO**.

    ![Konfiguracja uwierzytelniania użytkowników Netskope](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Na stronie **Dodawanie konta SAML** wykonaj następujące czynności:

    ![Konfiguracja uwierzytelniania użytkowników Netskope](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. W polu tekstowym **NAZWA** podaj nazwę, taką jak usługa Azure AD.

    b. W polu tekstowym **adresu URL usługi IDP** wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure portal.

    d. W polu tekstowym **identyfikatora jednostki IDP** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    d. Otwórz pobrany plik metadanych w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **CERTYFIKATU IDP.**

    e. Kliknij **przycisk ZAPISZ**.

### <a name="create-netskope-user-authentication-test-user"></a>Utwórz użytkownika testowego netskope user authentication

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy Netskope User Authentication jako administrator.

1. Kliknij kartę **Ustawienia** z lewego okienka nawigacji.

    ![Tworzenie użytkownika uwierzytelniania użytkowników netskope](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Kliknij kartę **Aktywna platforma.**

    ![Tworzenie użytkownika uwierzytelniania użytkowników netskope](./media/netskope-user-authentication-tutorial/user1.png)

1. Kliknij kartę **Użytkownicy.**

    ![Tworzenie użytkownika uwierzytelniania użytkowników netskope](./media/netskope-user-authentication-tutorial/add-user.png)

1. Kliknij **pozycję DODAJ UŻYTKOWNIKÓW**.

    ![Tworzenie użytkownika uwierzytelniania użytkowników netskope](./media/netskope-user-authentication-tutorial/user-add.png)

1. Wprowadź adres e-mail użytkownika, którego chcesz dodać, a następnie kliknij przycisk **ADD**.

    ![Tworzenie użytkownika uwierzytelniania użytkowników netskope](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Netskope User Authentication w Panelu dostępu należy automatycznie zalogować się do uwierzytelniania użytkownika Netskope, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj uwierzytelnianie użytkowników netskope za pomocą usługi Azure AD](https://aad.portal.azure.com/)