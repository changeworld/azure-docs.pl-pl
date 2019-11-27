---
title: 'Samouczek: integracja Azure Active Directory z usługą Egnyte | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7201b7d3f9f7e8333b18a087f58b6211101b1304
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233556"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Samouczek: integracja Azure Active Directory z usługą Egnyte

Z tego samouczka dowiesz się, jak zintegrować aplikację Egnyte z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji Egnyte z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Egnyte.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Egnyte (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Egnyte, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Egnyte z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Egnyte obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-egnyte-from-the-gallery"></a>Dodawanie aplikacji Egnyte z galerii

Aby skonfigurować integrację aplikacji Egnyte z usługą Azure AD, musisz dodać aplikację Egnyte z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Egnyte z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Egnyte** wybierz pozycję **Egnyte** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Egnyte na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem aplikacji Egnyte.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego aplikacji Egnyte](#configure-egnyte-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Egnyte](#create-egnyte-test-user)** — aby udostępnić w aplikacji Egnyte odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją Egnyte, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Egnyte** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Egnyte](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj wartość przy użyciu rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Egnyte](https://www.egnyte.com/corp/contact_egnyte.html), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

5. W sekcji **Konfigurowanie aplikacji Egnyte** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-egnyte-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Egnyte

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Egnyte jako administrator.

2. Kliknij pozycję **Ustawienia**.
   
    ![Ustawienia](./media/egnyte-tutorial/ic787819.png "Ustawienia")

3. W menu kliknij pozycję **Settings (Ustawienia)** .

    ![Ustawienia](./media/egnyte-tutorial/ic787820.png "Ustawienia")

4. Kliknij kartę **Configuration (Konfiguracja)** , a następnie kliknij pozycję **Security (Zabezpieczenia)** .

    ![Bezpieczeństwo](./media/egnyte-tutorial/ic787821.png "Bezpieczeństwo")

5. W sekcji **Single Sign-On Authentication (Uwierzytelnianie logowania jednokrotnego)** wykonaj następujące kroki:

    ![Uwierzytelnianie logowania jednokrotnego](./media/egnyte-tutorial/ic787822.png "Uwierzytelnianie logowania jednokrotnego")   
    
    a. Dla pozycji **Single sign-on authentication (Uwierzytelnianie logowania jednokrotnego)** wybierz pozycję **SAML 2.0**.
   
    b. Dla pozycji **Identity provider (Dostawca tożsamości)** wybierz pozycję **AzureAD**.
   
    c. Wklej **adres URL logowania** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider login URL (Adres URL logowania dostawcy tożsamości)** .
   
    d. Wklej **identyfikator usługi Azure AD** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider entity ID (Identyfikator jednostki dostawcy tożsamości)** .
      
    e. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Identity provider certificate (Certyfikat dostawcy tożsamości)** .
   
    f. Dla pozycji **Default user mapping (Domyślne mapowanie użytkownika)** wybierz pozycję **Email address (Adres e-mail)** .
   
    g. Dla pozycji **Use domain-specific issuer value (Użyj wartości wystawcy specyficznej dla domeny)** wybierz pozycję **disabled (wyłączone)** .
   
    h. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Egnyte.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Egnyte**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Egnyte**.

    ![Link aplikacji Egnyte na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-egnyte-test-user"></a>Tworzenie użytkownika testowego aplikacji Egnyte

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Egnyte, musisz ich aprowizować w tej aplikacji. W przypadku aplikacji Egnyte aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do swojej firmowej witryny aplikacji **Egnyte** jako administrator.

2. Przejdź do pozycji **Settings (Ustawienia) \> Users & Groups (Użytkownicy i grupy)** .

3. Kliknij pozycję **Add New User (Dodaj nowego użytkownika)** , a następnie wybierz typ użytkownika, którego chcesz dodać.
   
    ![Użytkownicy](./media/egnyte-tutorial/ic787824.png "Użytkownicy")

4. W sekcji **New Power User (Nowy użytkownik zaawansowany)** wykonaj następujące kroki:
    
    ![Nowy użytkownik standardowy](./media/egnyte-tutorial/ic787825.png "Nowy użytkownik standardowy")   

    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np. **Brittasimon\@contoso.com**.

    b. W polu tekstowym **Username (Nazwa użytkownika)** wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    c. Wybierz pozycję **Single Sign-On (Logowanie jednokrotne)** dla pozycji **Authentication Type (Typ uwierzytelniania)** .
   
    d. Kliknij pozycję **Zapisz**.
    
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z powiadomieniem.
    >

>[!NOTE]
>Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników Egnyte i interfejsów API udostępnionych przez usługę Egnyte.
>

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Egnyte na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Egnyte, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

