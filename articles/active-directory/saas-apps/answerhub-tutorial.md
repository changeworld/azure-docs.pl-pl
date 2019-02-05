---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją AnswerHub | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 41a1eef4ff417890114addcac00e2df3e49dc529
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453267"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją AnswerHub

Z tego samouczka dowiesz się, jak zintegrować aplikację AnswerHub z usługą Azure Active Directory (Azure AD).
Integracja aplikacji AnswerHub z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji AnswerHub.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji AnswerHub (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją AnswerHub, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji AnswerHub z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja AnswerHub obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-answerhub-from-the-gallery"></a>Dodawanie aplikacji AnswerHub z galerii

Aby skonfigurować integrację aplikacji AnswerHub z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację AnswerHub z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **AnswerHub**, wybierz pozycję **AnswerHub** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja AnswerHub na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją AnswerHub, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji AnswerHub.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji AnswerHub, należy wykonać kroki opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji AnswerHub](#configure-answerhub-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji AnswerHub](#create-answerhub-test-user)** — aby mieć w aplikacji AnswerHub odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji AnswerHub, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **AnswerHub** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji AnswerHub — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company>.answerhub.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<company>.answerhub.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji AnswerHub](mailto:success@answerhub.com) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji AnswerHub** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-answerhub-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji AnswerHub

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji AnswerHub jako administrator.

    > [!NOTE]
    > Jeśli potrzebujesz pomocy związanej z konfigurowaniem aplikacji AnswerHub, skontaktuj się z [zespołem pomocy technicznej aplikacji AnswerHub](mailto:success@answerhub.com.).

2. Przejdź do sekcji **Administration** (Administracja).

3. Kliknij kartę **User and Group** (Użytkownik i grupa).

4. W okienku nawigacji po lewej stronie w sekcji **Social Settings** (Ustawienia społecznościowe) kliknij pozycję **SAML Setup** (Konfiguracja protokołu SAML).

5. Kliknij kartę **IDP Config** (Konfiguracja dostawcy tożsamości).

6. Na karcie **IDP Config** (Konfiguracja dostawcy tożsamości) wykonaj następujące kroki:

    ![Konfiguracja protokołu SAML](./media/answerhub-tutorial/ic785172.png "Konfiguracja protokołu SAML")  
  
    a. W polu tekstowym **IDP Login URL** (Adres URL logowania dostawcy tożsamości) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.
  
    b. W polu tekstowym **IDP Logout URL** (Adres URL wylogowywania dostawcy tożsamości) wklej wartość pola **Adres URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **IDP Name Identifier Format** (Format identyfikatora nazwy dostawcy tożsamości) wprowadź taką samą wartość identyfikator użytkownika, jak ta wybrana w witrynie Azure Portal w sekcji **Atrybuty użytkownika**.
  
    d. Kliknij pozycję **Keys and Certificates** (Klucze i certyfikaty).

7. Na karcie **Keys and Certificates** (Klucze i certyfikaty) wykonaj następujące kroki:

    ![Klucze i certyfikaty](./media/answerhub-tutorial/ic785173.png "Klucze i certyfikaty")  

    a. Otwórz w Notatniku certyfikat zakodowany w formacie Base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość do Schowka, a następnie wklej ją w polu tekstowym **IDP Public Key (x509 Format)** (Klucz publiczny dostawcy tożsamości w formacie x509).
  
    b. Kliknij pozycję **Zapisz**.

8. Na karcie **IDP Config** (Konfiguracja dostawcy tożsamości) kliknij przycisk **Save** (Zapisz).

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

W tej sekcji umożliwisz użytkownikowi Britta Simon korzystanie z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji AnswerHub.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **AnswerHub**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **AnswerHub**.

    ![Link do aplikacji AnswerHub na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-answerhub-test-user"></a>Tworzenie użytkownika testowego aplikacji AnswerHub

Aby umożliwić użytkownikom usługi Azure AD logowanie się w aplikacji AnswerHub, muszą być oni zaaprowizowani w tej aplikacji. W przypadku aplikacji AnswerHub aprowizacja jest zadaniem wykonywanym ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji **AnswerHub** jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

3. Kliknij kartę **Users & Groups** (Użytkownicy i grupy).

4. W okienku nawigacji po lewej stronie w sekcji **Manage Users** (Zarządzanie użytkownikami) kliknij pozycję **Create or import users** (Tworzenie lub importowanie użytkowników), a następnie kliknij pozycję **Users & Groups** (Użytkownicy i grupy).

   ![Użytkownicy i grupy](./media/answerhub-tutorial/ic785175.png "Użytkownicy i grupy")

5. W polach tekstowych **Email address** (Adres e-mail), **Username** (Nazwa użytkownika) i **Password** (Hasło) wpisz dane prawidłowego konta usługi Azure Active Directory, które chcesz zaaprowizować, a następnie kliknij przycisk **Save** (Zapisz).

> [!NOTE]
> Do aprowizacji kont użytkowników aplikacji AnswerHub możesz użyć jakichkolwiek innych narzędzi lub interfejsów API służących do tworzenia kont użytkowników dostępnych w aplikacji AnswerHub.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka AnswerHub na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji AnswerHub, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

