---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją Zoom | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca0e2c0ce12edba504745e2783844db5109ee01a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237710"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją Zoom

Z tego samouczka dowiesz się, jak zintegrować aplikację Zoom z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Zoom z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Zoom.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Zoom (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Zoom z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Zoom obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="adding-zoom-from-the-gallery"></a>Dodawanie aplikacji Zoom z galerii

Aby skonfigurować integrację aplikacji Zoom w usłudze Azure AD, należy dodać aplikację Zoom z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Zoom z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zoom**, wybierz pozycję **Zoom** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Zoom na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Zoom, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Zoom.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD przy użyciu aplikacji Zoom, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Zoom](#configure-zoom-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Zoom](#create-zoom-test-user)** — aby w aplikacji Zoom utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD przy użyciu aplikacji Zoom, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Zoom** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Zoom — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.zoom.us`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `<companyname>.zoom.us`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zoom](https://support.zoom.us/hc/en-us) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Zoom oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:
    
    | Name (Nazwa) | Przestrzeń nazw  |  Atrybut źródłowy|
    | ---------------| --------------- | --------- |
    | Adres e-mail  | user.mail  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail |
    | Imię  | user.givenname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |
    | Nazwisko  | user.surname  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |
    | Numer telefonu  | user.telephonenumber  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone |
    | Dział  | user.department  | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department |
    | role (rola) |    user.assignedrole |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role |

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    d. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    e. Kliknij przycisk **OK**.

    f. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Aplikacja Zoom może oczekiwać oświadczenia grupy w ładunku SAML, dlatego jeśli utworzono dowolną grupę, skontaktuj się z [zespołem pomocy technicznej klienta Zoom](https://support.zoom.us/hc/en-us) i podaj informacje dotyczące grupy, aby zespół ten mógł również skonfigurować te informacje po swojej stronie. Musisz również podać [zespołowi pomocy technicznej klienta Zoom](https://support.zoom.us/hc/en-us) identyfikator obiektu, aby członkowie zespołu mogli go skonfigurować po swojej stronie. W celu uzyskania identyfikatora obiektu postępuj zgodnie z opisem w tym [dokumencie](https://support.zoom.us/hc/en-us/articles/115005887566).

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **Konfigurowanie aplikacji Zoom** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-zoom-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Zoom

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Zoom jako administrator.

2. Kliknij kartę **Single Sign-On** (Logowanie jednokrotne).

    ![Karta Single Sign-On (Logowanie jednokrotne)](./media/zoom-tutorial/ic784700.png "Logowanie jednokrotne")

3. Kliknij kartę **Security Control** (Kontrola zabezpieczeń), a następnie przejdź do ustawień **Single Sign-On** (Logowanie jednokrotne).

4. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Sekcja Single Sign-On (Logowanie jednokrotne)](./media/zoom-tutorial/ic784701.png "Logowanie jednokrotne")

    a. W polu tekstowym **Sign-in page URL** (Adres URL strony logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Sign-out page URL** (Adres URL strony wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. Otwórz certyfikat kodowany algorytmem base-64 w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w zawartość w polu tekstowym **Certyfikat dostawcy tożsamości**.

    d. W polu tekstowym **Wystawca** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal. 

    e. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Aby dowiedzieć się więcej, zapoznaj się z dokumentacją aplikacji Zoom pod adresem [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Zoom.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Zoom**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz ciąg **Zoom**.

    ![Link aplikacji Zoom na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zoom-test-user"></a>Tworzenie użytkownika testowego aplikacji Zoom

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Zoom, należy aprowizować ich w aplikacji Zoom. W przypadku aplikacji Zoom aprowizowanie to zadanie wykonywane ręcznie.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny aplikacji **Zoom** jako administrator.

2. Kliknij kartę **Account Management** (Zarządzanie kontami), a następnie kliknij pozycję **User Management** (Zarządzanie użytkownikami).

3. W sekcji User Management (Zarządzanie użytkownikami) kliknij pozycję **Add users** (Dodaj użytkowników).

    ![Zarządzanie użytkownikami](./media/zoom-tutorial/ic784703.png "Zarządzanie użytkownikami")

4. Na stronie **Add users** (Dodawanie użytkowników) wykonaj następujące czynności:

    ![Dodawanie użytkowników](./media/zoom-tutorial/ic784704.png "Dodawanie użytkowników")

    a. W polu **User Type** (User Type) wybierz pozycję **Basic** (Podstawowy).

    b. W polu tekstowym **Emails** (Adresy e-mail) wpisz adres e-mail prawidłowego konta usługi Azure AD, które chcesz aprowizować.

    d. Kliknij pozycję **Add** (Dodaj).

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure Active Directory możesz użyć dowolnego innego interfejsu API lub narzędzia do tworzenia kont użytkowników aplikacji Zoom oferowanego przez tę aplikację.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Zoom w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Zoom, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
