---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Envi MMIS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bc40c5043cefad7892eaead8fe8058ac3707865
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60279782"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Envi MMIS

Z tego samouczka dowiesz się, jak zintegrować aplikację Envi MMIS z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Envi MMIS z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Envi MMIS.
* Możesz zezwolić użytkownikom na automatyczne logowanie do aplikacji Envi MMIS (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Envi MMIS są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Envi MMIS z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Envi MMIS obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** i **dostawcę tożsamości**

## <a name="adding-envi-mmis-from-the-gallery"></a>Dodawanie aplikacji Envi MMIS z galerii

Aby skonfigurować integrację aplikacji Envi MMIS w usłudze Azure AD, należy dodać aplikację Envi MMIS z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Envi MMIS z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Envi MMIS**, wybierz pozycję **Envi MMIS** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Envi MMIS na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Envi MMIS, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem aplikacji Envi MMIS.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Envi MMIS, należy wykonać kroki opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Envi MMIS](#configure-envi-mmis-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Envi MMIS](#create-envi-mmis-test-user)** — aby udostępnić w aplikacji Envi MMIS odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Envi MMIS, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Envi MMIS** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Envi MMIS](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Envi MMIS](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji Envi MMIS](mailto:support@ioscorp.com), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie aplikacji Envi MMIS** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-envi-mmis-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Envi MMIS

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Envi MMIS jako administrator.

2. Kliknij kartę **My Domain (Moja domena)**.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/envimmis-tutorial/configure1.png)

3. Kliknij pozycję **Edytuj**.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/envimmis-tutorial/configure2.png)

4. Zaznacz pole wyboru **Use remote authentication (Użyj zdalnego uwierzytelniania)**, a następnie wybierz pozycję **HTTP Redirect (Przekierowanie HTTP)** z listy rozwijanej **Authentication Type (Typ uwierzytelniania)**.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/envimmis-tutorial/configure3.png)

5. Wybierz kartę **Resources (Zasoby)**, a następnie kliknij pozycję **Upload Metadata (Przekaż metadane)**.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/envimmis-tutorial/configure4.png)

6. W menu podręcznym **Upload Metadata (Przekaż metadane)** wykonaj następujące kroki:

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/envimmis-tutorial/configure5.png)

    a. Wybierz opcję **File (Plik)** z listy rozwijanej **Upload From (Przekaż z)**.

    b. Przekaż plik metadanych pobrany z witryny Azure Portal, wybierając **ikonę wyboru pliku**.

    c. Kliknij przycisk **OK**.

7. Po przekazaniu pobranego pliku metadanych pola zostaną automatycznie wypełnione. Kliknij pozycję **Update** (Aktualizuj)

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Envi MMIS.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Envi MMIS**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Envi MMIS**.

    ![Link aplikacji Envi MMIS na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-envi-mmis-test-user"></a>Tworzenie użytkownika testowego aplikacji Envi MMIS

Aby umożliwić użytkownikom usługi Azure AD logowanie się w aplikacji Envi MMIS, należy ich aprowizować w tej aplikacji. W przypadku aplikacji Envi MMIS aprowizacja to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji Envi MMIS jako administrator.

2. Kliknij kartę **User List (Lista użytkowników)**.

    ![Dodawanie pracownika](./media/envimmis-tutorial/user1.png)

3. Kliknij przycisk **Add User (Dodaj użytkownika)**.

    ![Dodawanie pracownika](./media/envimmis-tutorial/user2.png)

4. W sekcji **Add User (Dodaj użytkownika)** wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/envimmis-tutorial/user3.png)

    a. W **nazwa_użytkownika** pola tekstowego, typ nazwy użytkownika konta Britta Simon, takich jak **brittasimon\@contoso.com**.
    
    b. W polu tekstowym **First Name (Imię)** wpisz imię użytkownika, na przykład **Britta**.

    c. W polu tekstowym **Last Name (Nazwisko)** wpisz nazwisko użytkownika, na przykład **Simon**.

    d. Wprowadź tytuł użytkownika w polu tekstowym **Title (Tytuł)**.
    
    e. W **adres E-mail** polu tekstowym wpisz adres e-mail konta Britta Simon, takich jak **brittasimon\@contoso.com**.

    f. W **nazwa użytkownika logowania jednokrotnego** pola tekstowego, typ nazwy użytkownika konta Britta Simon, takich jak **brittasimon\@contoso.com**.

    g. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Envi MMIS w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Envi MMIS, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

