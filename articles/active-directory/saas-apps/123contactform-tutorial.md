---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją 123ContactForm | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 768afe007141eacbcea1560d3ec1676d9a7d2867
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65859571"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją 123ContactForm

Z tego samouczka dowiesz się, jak zintegrować aplikację 123ContactForm z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji 123ContactForm z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji 123ContactForm.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie się do aplikacji 123ContactForm (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją 123ContactForm, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji 123ContactForm z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja 123ContactForm obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**
* Aplikacja 123ContactForm obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-123contactform-from-the-gallery"></a>Dodawanie aplikacji 123ContactForm z galerii

Aby skonfigurować integrację aplikacji 123ContactForm z usługą Azure AD, musisz dodać tę aplikację z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację 123ContactForm z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **123ContactForm**, wybierz pozycję **123ContactForm** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja 123ContactForm na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją 123ContactForm, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem aplikacji 123ContactForm.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją 123ContactForm, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji 123ContactForm](#configure-123contactform-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji 123ContactForm](#create-123contactform-test-user)** — aby udostępnić w aplikacji 123ContactForm odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji 123ContactForm, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **123ContactForm** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji 123ContactForm](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji 123ContactForm](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości, używając rzeczywistych adresów URL i identyfikatora, co zostało opisane w dalszej części tego samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie aplikacji 123ContactForm** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-123contactform-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji 123ContactForm

1. Aby skonfigurować logowanie jednokrotne po stronie aplikacji **123ContactForm**, przejdź na stronę [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/submit.png) 

    a. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **BrittaSimon\@Contoso.com**.

    b. Kliknij pozycję **Upload (Przekazywanie)** i przejdź do pobranego pliku XML metadanych, który został pobrany z witryny Azure Portal.

    c. Kliknij pozycję **SUBMIT FORM (PRZEŚLIJ FORMULARZ)**.

2. Na stronie **Microsoft Azure AD - Single sign-on - Configure App Settings (Konfigurowanie ustawień aplikacji usługi Microsoft Azure AD — logowanie jednokrotne)** wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/123contactform-tutorial/url3.png)

    a. Jeśli chcesz skonfigurować aplikację w **trybie inicjowanym przez dostawcę tożsamości**, skopiuj wartość **IDENTIFIER (IDENTYFIKATOR)** dla swojego wystąpienia, a następnie wklej ją w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    b. Jeśli chcesz skonfigurować aplikację w **trybie inicjowanym przez dostawcę tożsamości**, skopiuj wartość **REPLY URL (ADRES URL ODPOWIEDZI)** dla swojego wystąpienia, a następnie wklej ją w polu tekstowym **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    c. Jeśli chcesz skonfigurować aplikację w **trybie inicjowanym przez dostawcę tożsamości**, skopiuj wartość **SIGN ON URL (ADRES URL LOGOWANIA)** dla swojego wystąpienia, a następnie wklej ją w polu tekstowym **Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji 123ContactForm.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **123ContactForm**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **123ContactForm**.

    ![Link aplikacji 123ContactForm na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-123contactform-test-user"></a>Tworzenie użytkownika testowego aplikacji 123ContactForm

W tej sekcji utworzysz w aplikacji 123ContactForm użytkownika o nazwie Britta Simon. Aplikacja 123ContactForm obsługuje aprowizację użytkowników typu just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji 123ContactForm, zostanie utworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka 123ContactForm w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji 123ContactForm, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)