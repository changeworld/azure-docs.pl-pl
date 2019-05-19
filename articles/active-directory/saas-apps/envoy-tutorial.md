---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Envoy | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Envoy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 590e7e301b33fdab4122c4b776ebc52f6ed92b08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65862771"
---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją Envoy

Z tego samouczka dowiesz się, jak zintegrować aplikację Envoy z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Envoy z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować za pomocą usługi Azure AD, kto ma dostęp do aplikacji Envoy.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Envoy (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Envoy, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Envoy z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Envoy obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

* Aplikacja Envoy obsługuje aprowizację użytkowników **Just In Time**

## <a name="adding-envoy-from-the-gallery"></a>Dodawanie aplikacji Envoy z galerii

Aby skonfigurować integrację aplikacji Envoy z usługą Azure AD, należy dodać aplikację Envoy z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Envoy z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Envoy**, wybierz pozycję **Envoy** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Envoy na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Envoy, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Envoy.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Envoy, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Envoy](#configure-envoy-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Envoy](#create-envoy-test-user)** — aby mieć w aplikacji Envoy odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Envoy, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Envoy** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Envoy](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Envoy](https://envoy.com/contact/), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **Odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Konfigurowanie aplikacji Envoy** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-envoy-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze Envoy

1. W innym oknie przeglądarki internetowej zaloguj się jako administrator do witryny firmowej aplikacji Envoy.

2. Na pasku narzędzi u góry kliknij pozycję **Settings** (Ustawienia).

    ![Envoy](./media/envoy-tutorial/ic776782.png "Envoy")

3. Kliknij pozycję **Company** (Firma).

    ![Firma](./media/envoy-tutorial/ic776783.png "Firma")

4. Kliknij pozycję **SAML**.

    ![SAML](./media/envoy-tutorial/ic776784.png "SAML")

5. W sekcji konfiguracyjnej **SAML Authentication** (Uwierzytelnianie SAML) wykonaj następujące kroki:

    ![Uwierzytelnianie SAML](./media/envoy-tutorial/ic776785.png "Uwierzytelnianie SAML")
    
    >[!NOTE]
    >Wartość dla identyfikatora lokalizacji centrali jest automatycznie generowana przez aplikację.
    
    a. W polu tekstowym **Fingerprint** (Odcisk palca) wklej wartość pola **Odcisk palca** certyfikatu skopiowaną z witryny Azure Portal.
    
    b. W polu tekstowym **IDENTITY PROVIDER HTTP SAML URL** (ADRES URL SAML HTTP DOSTAWCY TOŻSAMOŚCI) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.
    
    c. Kliknij pozycję **Save changes** (Zapisz zmiany).

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Envoy.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Envoy**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Envoy**.

    ![Link do aplikacji Envoy na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-envoy-test-user"></a>Tworzenie użytkownika testowego aplikacji Envoy

Nie istnieje element akcji na potrzeby konfigurowania aprowizacji użytkowników w aplikacji Envoy. Gdy przypisany użytkownik próbuje zalogować się do aplikacji Envoy przy użyciu panelu dostępu, aplikacja Envoy sprawdza, czy użytkownik istnieje. Jeśli konto użytkownika nie jest jeszcze dostępne, jest ono automatycznie tworzone za pomocą aplikacji Envoy.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Envoy w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Envoy, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

