---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą WEDO | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą WEDO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 73adc94e-7656-4a92-99e3-a401c67be477
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ce7ffb389a585511883c3b35de3773ae37342b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76992373"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą WEDO

W tym samouczku dowiesz się, jak zintegrować WEDO z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi WEDO z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do usługi WEDO.
* Włącz użytkownikom automatyczne logowanie do usługi WEDO za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja włączona z logałem jednokrotnym WEDO. Skontaktuj się z [zespołem pomocy technicznej klienta WEDO,](mailto:info@wedo.swiss) aby uzyskać subskrypcję usługi SSO.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* WEDO obsługuje **sp i idp** zainicjowane SSO

* [Po skonfigurowaniu narzędzia WEDO można wymusić formanty sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>Dodawanie WEDO z galerii

Aby skonfigurować integrację usługi WEDO z usługą Azure AD, należy dodać usługę WEDO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **WEDO** w polu wyszukiwania.
1. Wybierz **WEDO** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi WEDO

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu usługi WEDO przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuował działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze WEDO.

Aby skonfigurować i przetestować sytuasz usługi Azure AD za pomocą usługi WEDO, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne WEDO](#configure-wedo-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego WEDO](#create-wedo-test-user)** — aby mieć odpowiednik B.Simon w WEDO, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **WEDO** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta WEDO,](mailto:info@wedo.swiss) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja WEDO oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | email | user.email |
    | firstName | user.firstName |
    | lastName | user.lasttName |
    | userName | nazwa użytkownika.userName |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie WEDO** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do WEDO.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **WEDO**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-wedo-sso"></a>Konfigurowanie sytcha wedo

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w usłudze WEDO.

1. Zaloguj się [w WEDO](https://login.wedo.swiss/). Musisz mieć **rolę administratora**.
1. W ustawieniach profilu wybierz menu **Uwierzytelnianie** w sekcji **Ustawienia sieci**.
1. Na stronie **Uwierzytelnianie SAML** wykonaj następujące czynności:

   ![Łącze uwierzytelniania SAML](media/wedo-tutorial/network-security-authentification.png)

   a. Włącz **uwierzytelnianie SAML**.

   b. Wybierz kartę **Metadane dostawcy tożsamości (XML).**

   d. Otwórz pobrany **kod XML metadanych federacyjnego** z witryny Azure portal do Notatnika i skopiuj zawartość metadanych XML i wklej je do pola tekstowego **certyfikatu X.509.**

   d. Kliknij pozycję **Zapisz**

### <a name="create-wedo-test-user"></a>Tworzenie użytkownika testowego WEDO

W tej sekcji utworzysz użytkownika testowego w WEDO o nazwie Bob Simon. Informacje muszą być zgodne z *tworzeniem użytkownika testowego usługi Azure AD*.

1. W ustawieniach Profil w udziale WEDO wybierz pozycję **Użytkownicy** z sekcji *Ustawienia sieciowe.*
1. Kliknij **pozycję Dodaj użytkownika**.
1. W wyskakującym okienku Dodaj użytkownika wypełnij informacje o użytkowniku

    a. Imię `B`.

    b. Nazwisko `Simon`.

    d. Wprowadź adres `username@companydomain.extension`e-mail . Na przykład `B.Simon@contoso.com`. Obowiązkowe jest, aby mieć e-mail z tej samej domeny co nazwa krótka firmy.

    d. Typ `User`użytkownika .

    e. Kliknij **pozycję Utwórz użytkownika**.

    f. Na stronie *Wybierz zespoły* kliknij pozycję **Zapisz**.

    g.  Na stronie *Zaproś użytkownika* kliknij przycisk **Tak**.

1. Sprawdzanie poprawności użytkownika przy użyciu łącza otrzymanego przez e-mail

> [!NOTE]
> Jeśli chcesz utworzyć fałszywego użytkownika (e-mail powyżej nie istnieje w twojej sieci), skontaktuj się z [naszym wsparciem,](mailto:info@wedo.swiss) aby zweryfikować użytkownika*.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka WEDO w Panelu dostępu należy automatycznie zalogować się do WEDO, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę WEDO w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić WEDO dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
