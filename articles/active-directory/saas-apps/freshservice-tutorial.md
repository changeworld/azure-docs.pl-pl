---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Freshservice | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: ab1d083d7fea59284815276d030181ac1e435f72
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820015"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Freshservice

Z tego samouczka dowiesz się, jak zintegrować aplikację Freshservice z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Freshservice z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Freshservice.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Freshservice (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Freshservice potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Freshservice z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Freshservice obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-freshservice-from-the-gallery"></a>Dodawanie aplikacji Freshservice z galerii

Aby skonfigurować integrację aplikacji Freshservice z usługą Azure AD, musisz dodać aplikację Freshservice z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Freshservice z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Freshservice**, wybierz pozycję **Freshservice** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Freshservice na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Freshservice, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Freshservice.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Freshservice, utwórz poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Freshservice](#configure-freshservice-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Freshservice](#create-freshservice-test-user)** — aby mieć w aplikacji Freshservice odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Freshservice, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Freshservice** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL aplikacji Freshservice na potrzeby logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<democompany>.freshservice.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Freshservice](https://support.freshservice.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **Odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie aplikacji Freshservice** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-freshservice-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Freshservice

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Freshservice jako administrator.

2. W menu u góry kliknij pozycję **Admin** (Administrator).

    ![Administrator](./media/freshservice-tutorial/ic790814.png "Administrator")

3. W obszarze **Customer Portal** (Portal klienta) kliknij pozycję **Security** (Zabezpieczenia).

    ![Zabezpieczenia](./media/freshservice-tutorial/ic790815.png "Zabezpieczenia")

4. W sekcji **Security** (Zabezpieczenia) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/freshservice-tutorial/ic790816.png "Logowanie jednokrotne")

    a. Włącz przełącznik **Single Sign On** (Logowanie jednokrotne).

    b. Wybierz pozycję **SAML SSO** (Logowanie jednokrotne SAML).

    d. W polu tekstowym **SAML Login URL** (Adres URL logowania SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym **Security Certificate Fingerprint** (Odcisk palca certyfikatu zabezpieczeń) wklej wartość **ODCISK PALCA** certyfikatu skopiowaną z witryny Azure Portal.

    f. Kliknij pozycję **Zapisz**

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Freshservice.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Freshservice**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Freshservice**.

    ![Link Freshservice na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-freshservice-test-user"></a>Tworzenie użytkownika testowego aplikacji Freshservice

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji FreshService, należy ich aprowizować w aplikacji FreshService. W przypadku aplikacji FreshService jest to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji **FreshService** jako administrator.

2. W menu u góry kliknij pozycję **Admin** (Administrator).

    ![Administrator](./media/freshservice-tutorial/ic790814.png "Administrator")

3. W sekcji **User Management** (Zarządzanie użytkownikami) kliknij pozycję **Requesters** (Inicjatorzy żądania).

    ![Inicjatorzy żądania](./media/freshservice-tutorial/ic790818.png "Inicjatorzy żądania")

4. Kliknij przycisk **New Requester** (Nowy inicjator żądania).

    ![Nowi inicjatorzy żądania](./media/freshservice-tutorial/ic790819.png "Nowi inicjatorzy żądania")

5. W sekcji **New Requester** (Nowy inicjator żądania) wykonaj następujące kroki:

    ![Nowy inicjator żądania](./media/freshservice-tutorial/ic790820.png "Nowy inicjator żądania")  

    a. W polach tekstowych **First Name** (Imię) i **Email** (Adres e-mail) wprowadź atrybuty imienia i adresu e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

    b. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.
    >  

> [!NOTE]
> Aby aprowizować konta użytkowników usługi AAD, można użyć jakichkolwiek innych narzędzi do tworzenia konta użytkownika aplikacji FreshService lub interfejsów API dostarczonych przez firmę FreshService.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka FreshService w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji FreshService, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
