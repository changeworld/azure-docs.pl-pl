---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Knowledge Anywhere LMS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: f39952c74006964155fd23920c85506cac13a878
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60261942"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Knowledge Anywhere LMS

Z tego samouczka dowiesz się, jak zintegrować aplikację Knowledge Anywhere LMS z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Knowledge Anywhere LMS z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Knowledge Anywhere LMS.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Knowledge Anywhere LMS (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Knowledge Anywhere LMS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Knowledge Anywhere LMS z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Knowledge Anywhere LMS obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**
* Aplikacja Knowledge Anywhere LMS obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Dodawanie aplikacji Knowledge Anywhere LMS z galerii

Aby skonfigurować integrację aplikacji Knowledge Anywhere LMS z usługą Azure AD, należy dodać aplikację Knowledge Anywhere LMS z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Knowledge Anywhere LMS z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **Knowledge Anywhere LMS**, wybierz pozycję **Knowledge Anywhere LMS** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Knowledge Anywhere LMS na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w aplikacji Knowledge Anywhere LMS, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Knowledge Anywhere LMS.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Knowledge Anywhere LMS, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Knowledge Anywhere LMS](#create-knowledge-anywhere-lms-test-user)** — aby mieć w aplikacji Knowledge Anywhere LMS odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Knowledge Anywhere LMS, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Knowledge Anywhere LMS** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL aplikacji Knowledge Anywhere LMS — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL odpowiedzi, co zostało opisane w dalszej części tego samouczka.

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Domena i adresy URL aplikacji Knowledge Anywhere LMS — informacje dotyczące logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie aplikacji Knowledge Anywhere LMS** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Knowledge Anywhere LMS

1. W innym oknie przeglądarki otwórz portal administracyjny aplikacji Knowledge Anywhere LMS.

2. Wybierz kartę **Site** (Witryna).

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. Wybierz kartę **SAML Settings** (Ustawienia języka SAML).

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. Kliknij pozycję **Add New** (Dodaj nowe).

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. Na stronie **Add/Update SAML Settings** (Dodawanie/aktualizowanie ustawień języka SAML), wykonaj następujące czynności:

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Wprowadź nazwę dostawcy tożsamości zgodnie z wymaganiami organizacji. Na przykład:- `Azure`.

    b. W polu tekstowym **IDP Entity ID** (Identyfikator jednostki dostawcy tożsamości) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **IDP URL** (Adres URL dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. Otwórz plik certyfikatu pobrany z witryny Azure Portal do Notatnika, skopiuj zawartość certyfikatu i wklej go w polu tekstowym **Certificate** (Certyfikat).

    e. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość pola **Adres URL wylogowywania** skopiowaną z witryny Azure Portal.

    f. Wybierz pozycję **Main Site** (Witryna główna) z listy rozwijanej w polu **Domain** (Domena).

    g. Skopiuj wartość **SP Entity ID** (Identyfikator jednostki dostawcy usługi) i wklej ją w polu tekstowym **Identifier** (Identyfikator) w sekcji **Podstawowa konfiguracja protokołu SAML** witryny Azure Portal.

    h. Skopiuj wartość **SP Response(ACS) URL** (Adres URL odpowiedzi dostawcy usługi (ACS)) i wklej go w polu tekstowym **Adres URL odpowiedzi** sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    i. Kliknij pozycję **Zapisz**.

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

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Knowledge Anywhere LMS.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Knowledge Anywhere LMS**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Knowledge Anywhere LMS**.

    ![Link do aplikacji Knowledge Anywhere LMS na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Tworzenie użytkownika testowego aplikacji Knowledge Anywhere LMS

W tej sekcji w aplikacji Knowledge Anywhere LMS jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Knowledge Anywhere LMS obsługuje aprowizację użytkowników typu „just in time”, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Knowledge Anywhere LMS, zostanie utworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Knowledge Anywhere LMS na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi Knowledge Anywhere LMS, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)