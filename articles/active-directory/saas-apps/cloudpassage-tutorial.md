---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją CloudPassage | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b94d852ec5e0cba133fc1e141a5695e1c898f16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105344"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją CloudPassage

Z tego samouczka dowiesz się, jak zintegrować aplikację CloudPassage z usługą Azure Active Directory (Azure AD).
Integracja aplikacji CloudPassage z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji CloudPassage.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji CloudPassage (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją CloudPassage, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji CloudPassage z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja CloudPassage obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-cloudpassage-from-the-gallery"></a>Dodawanie aplikacji CloudPassage z galerii

Aby skonfigurować integrację aplikacji CloudPassage z usługą Azure AD, musisz dodać aplikację CloudPassage z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację CloudPassage z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **CloudPassage**, wybierz pozycję **CloudPassage** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja CloudPassage na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją CloudPassage, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji CloudPassage.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją CloudPassage, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji CloudPassage](#configure-cloudpassage-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji CloudPassage](#create-cloudpassage-test-user)** — aby mieć w aplikacji CloudPassage odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji CloudPassage, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **CloudPassage** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji CloudPassage — informacje dotyczące logowania jednokrotnego](common/sp-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://portal.cloudpassage.com/saml/init/accountid`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://portal.cloudpassage.com/saml/consume/accountid`. Wartość tego atrybutu można uzyskać, klikając pozycję **Dokumentacja dotycząca konfiguracji logowania jednokrotnego** w sekcji **Ustawienia logowania jednokrotnego** witryny CloudPassage.

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem obsługi klienta aplikacji CloudPassage](https://www.cloudpassage.com/company/contact/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja CloudPassage oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Name (Nazwa) | Atrybut źródłowy|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **Konfigurowanie aplikacji CloudPassage** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-cloudpassage-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji CloudPassage

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji CloudPassage jako administrator.

1. W menu u góry kliknij pozycję **Settings (Ustawienia)** , a następnie **Site Administration (Administrowanie witryną)** . 
   
    ![Konfigurowanie logowania jednokrotnego][12]

1. Kliknij kartę **Authentication Settings (Ustawienia uwierzytelniania)** . 
   
    ![Konfigurowanie logowania jednokrotnego][13]

1. W sekcji **Single Sign-on Settings (Ustawienia logowania jednokrotnego)** wykonaj następujące kroki: 
   
    ![Konfigurowanie logowania jednokrotnego][14]

    a. Zaznacz pole wyboru **Enable Single sign-on(SSO)(SSO Setup Documentation) (Włącz logowanie jednokrotne[SSO] [Dokumentacja dotycząca konfiguracji logowania jednokrotnego])** .
    
    b. Wklej **identyfikator usługi Azure AD** w polu tekstowym **SAML issuer URL (Adres URL wystawcy SAML)** .
  
    c. Wklej **adres URL logowania** w polu tekstowym **SAML endpoint URL (Adres URL punktu końcowego SAML)** .
  
    d. Wklej **adres URL wylogowywania** w polu tekstowym **Logout landing page (Strona docelowa wylogowywania)** .
  
    e. Otwórz pobrany plik certyfikatu w Notatniku, skopiuj zawartość pobranego certyfikatu do schowka, a następnie wklej ją w polu tekstowym **x 509 certificate (Certyfikat x 509)** .
  
    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji CloudPassage.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **CloudPassage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **CloudPassage**.

    ![Link aplikacji CloudPassage na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-cloudpassage-test-user"></a>Tworzenie użytkownika testowego aplikacji CloudPassage

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji CloudPassage.

**Aby utworzyć użytkownika o nazwie Britta Simon w aplikacji CloudPassage, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji **CloudPassage** jako administrator. 

1. Na pasku narzędzi u góry kliknij pozycję **Settings (Ustawienia)** , a następnie **Site Administration (Administrowanie witryną)** . 
   
    ![Tworzenie użytkownika testowego aplikacji CloudPassage][22] 

1. Kliknij kartę **Users (Użytkownicy)** , a następnie kliknij pozycję **Add New User (Dodaj nowego użytkownika)** . 
   
    ![Tworzenie użytkownika testowego aplikacji CloudPassage][23]

1. W sekcji **Add New User (Dodaj nowego użytkownika)** wykonaj następujące kroki: 
   
    ![Tworzenie użytkownika testowego aplikacji CloudPassage][24]
    
    a. W polu tekstowym **First Name (Imię)** wpisz Britta. 
  
    b. W polu tekstowym **Last Name (Nazwisko)** wpisz Simon.
  
    c. W polu tekstowym **Username (Nazwa użytkownika)** , polu tekstowym **Email (Adres e-mail)** i polu tekstowym **Retype Email (Wpisz ponownie adres e-mail)** wpisz nazwę użytkownika Britta w usłudze Azure AD.
  
    d. Jako **typ dostępu** wybierz opcję **Enable Halo Portal Access (Włącz dostęp z poziomu portalu Halo)** .
  
    e. Kliknij pozycję **Add** (Dodaj).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka CloudPassage na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji CloudPassage, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

