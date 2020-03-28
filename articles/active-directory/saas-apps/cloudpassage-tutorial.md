---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z CloudPassage | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0bcf0b4781e7ecb4301672f511a9f01f7fd2082
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561232"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z cloudpassage

W tym samouczku dowiesz się, jak zintegrować CloudPassage z usługą Azure Active Directory (Azure AD). Po zintegrowaniu CloudPassage z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do CloudPassage.
* Włącz użytkownikom automatyczne logowanie się do usługi CloudPassage za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej CloudPassage(SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja CloudPassage obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-cloudpassage-from-the-gallery"></a>Dodawanie aplikacji CloudPassage z galerii

Aby skonfigurować integrację aplikacji CloudPassage z usługą Azure AD, musisz dodać aplikację CloudPassage z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **CloudPassage** w polu wyszukiwania.
1. Wybierz **CloudPassage** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi CloudPassage

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą cloudpassage przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sygnowania użytkownika działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w cloudpassage.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą cloudpassage, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj cloudpassage SSO](#configure-cloudpassage-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego CloudPassage](#create-cloudpassage-test-user)** — aby mieć odpowiednik B.Simon w CloudPassage, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **CloudPassage** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

     a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://portal.cloudpassage.com/saml/init/accountid`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://portal.cloudpassage.com/saml/consume/accountid`. Wartość tego atrybutu można uzyskać, klikając pozycję **Dokumentacja dotycząca konfiguracji logowania jednokrotnego** w sekcji **Ustawienia logowania jednokrotnego** witryny CloudPassage.

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem obsługi klienta aplikacji CloudPassage](https://www.cloudpassage.com/company/contact/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. CloudPassage aplikacja oczekuje potwierdzeń SAML w określonym formacie, który wymaga, aby dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji CloudPassage oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie CloudPassage** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do CloudPassage.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **CloudPassage**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-cloudpassage-sso"></a>Konfigurowanie usługi SSO CloudPassage

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji CloudPassage jako administrator.

1. W menu u góry kliknij pozycję **Settings (Ustawienia)**, a następnie **Site Administration (Administrowanie witryną)**. 
   
    ![Konfigurowanie logowania jednokrotnego][12]

1. Kliknij kartę **Authentication Settings (Ustawienia uwierzytelniania)**. 
   
    ![Konfigurowanie logowania jednokrotnego][13]

1. W sekcji **Single Sign-on Settings (Ustawienia logowania jednokrotnego)** wykonaj następujące kroki: 
   
    ![Konfigurowanie logowania jednokrotnego][14]

    a. Zaznacz pole wyboru **Enable Single sign-on(SSO)(SSO Setup Documentation) (Włącz logowanie jednokrotne[SSO] [Dokumentacja dotycząca konfiguracji logowania jednokrotnego])**.
    
    b. Wklej **identyfikator usługi Azure AD** do pola tekstowego adresu URL **wystawcy SAML.**
  
    d. Wklej **adres URL logowania** w polu tekstowym **SAML endpoint URL (Adres URL punktu końcowego SAML)**.
  
    d. Wklej **adres URL wylogowywania** w polu tekstowym **Logout landing page (Strona docelowa wylogowywania)**.
  
    e. Otwórz pobrany plik certyfikatu w Notatniku, skopiuj zawartość pobranego certyfikatu do schowka, a następnie wklej ją w polu tekstowym **x 509 certificate (Certyfikat x 509)**.
  
    f. Kliknij przycisk **Zapisz**.

### <a name="create-cloudpassage-test-user"></a>Tworzenie użytkownika testowego aplikacji CloudPassage

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w CloudPassage.

**Aby utworzyć użytkownika o nazwie B.Simon w CloudPassage, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji **CloudPassage** jako administrator. 

1. Na pasku narzędzi u góry kliknij pozycję **Settings (Ustawienia)**, a następnie **Site Administration (Administrowanie witryną)**. 
   
    ![Tworzenie użytkownika testowego aplikacji CloudPassage][22] 

1. Kliknij kartę **Users (Użytkownicy)**, a następnie kliknij pozycję **Add New User (Dodaj nowego użytkownika)**. 
   
    ![Tworzenie użytkownika testowego aplikacji CloudPassage][23]

1. W sekcji **Add New User (Dodaj nowego użytkownika)** wykonaj następujące kroki: 
   
    ![Tworzenie użytkownika testowego aplikacji CloudPassage][24]
    
    a. W polu tekstowym **First Name (Imię)** wpisz Britta. 
  
    b. W polu tekstowym **Last Name (Nazwisko)** wpisz Simon.
  
    d. W polu tekstowym **Username (Nazwa użytkownika)**, polu tekstowym**Email (Adres e-mail)** i polu tekstowym **Retype Email (Wpisz ponownie adres e-mail)** wpisz nazwę użytkownika Britta w usłudze Azure AD.
  
    d. Jako **typ dostępu** wybierz opcję **Enable Halo Portal Access (Włącz dostęp z poziomu portalu Halo)**.
  
    e. Kliknij przycisk **Dodaj**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka CloudPassage na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji CloudPassage, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj CloudPassage z usługą Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

