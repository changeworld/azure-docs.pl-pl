---
title: 'Samouczek: Integracja usługi Azure Active Directory z muzyką rozliczania | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a muzyką rozliczania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08f084bc0231bceeaf2c7d87dc0a160cdf188978
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090980"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Samouczek: Integracja usługi Azure Active Directory z muzyką rozliczania

W tym samouczku dowiesz się, jak zintegrować muzykę rozliczania z usługą Azure Active Directory (Azure AD).
Integracja rozliczania muzyki z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do rozliczania muzyki.
* Można włączyć użytkowników, aby automatycznie zalogować się do rozliczania muzyki (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z muzyką rozliczania, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Rozliczanie subskrypcji z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługa muzyki osadzającej **SP** zainicjowana przez firmę SSO

## <a name="adding-settling-music-from-the-gallery"></a>Dodawanie muzyki osadzającej z galerii

Aby skonfigurować integrację rozliczania muzyki z usługą Azure AD, należy dodać muzykę rozliczania z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać muzykę rozliczania z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Settle muzykę**, wybierz pozycję **Rozliczanie muzyki** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Rozliczanie muzyki na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z rozliczania muzyki na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Ustalania muzyki.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą muzyki rozliczania, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj muzykę rozliczania Logowanie jednokrotne](#configure-settling-music-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego muzyki rozliczenia](#create-settling-music-test-user)** — aby mieć odpowiednik Britta Simon w rozliczania muzyki, która jest połączona z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą muzyki rozliczania, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **muzycznej Rozliczanie** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Rozliczanie muzyki Informacje o domenie i adresach URL logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta ustalania muzyki,](https://rakurakuseisan.jp/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie muzyki rozliczania** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-settling-music-single-sign-on"></a>Konfigurowanie rozliczania muzyki Logowanie jednokrotne

1. W innym oknie przeglądarki internetowej zaloguj się do rozliczenia muzyki jako administrator zabezpieczeń.

1. U góry strony kliknij kartę **zarządzanie.**

    ![Rozliczanie muzyki krok 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Kliknij kartę **Ustawienia systemowe.**

    ![Osiedlanie muzyki krok2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Przełącz się na kartę **Zabezpieczenia.**

    ![Osiadlanie krok muzyki3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. W sekcji **Ustawień logowania jednokrotnego** wykonaj następujące czynności:

    ![Osiadlanie krok muzyki5](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Kliknij **pozycję Aby włączyć**opcję .

    b. W **adresie URL logowania dostawcy** identyfikatora pole tekstowe, wklej wartość **adresu URL logowania,** które zostały skopiowane z witryny Azure portal.

    d. W polu tekstowym **adresu URL wylogowania dostawcy identyfikatorów** wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure portal.

    d. Kliknij **pozycję Wybierz plik,** aby przekazać **certyfikat (Base64),** który został pobrany w witrynie Azure portal.

    e. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** `brittasimon@yourcompanydomain.extension`użytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do rozliczania muzyki.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Rozliczanie muzyki**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Rozliczanie muzyki**.

    ![Łącze Muzyka rozliczania na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-settling-music-test-user"></a>Tworzenie użytkownika testowego muzyki rozliczania

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w rozliczaniu muzyki. Współpracuj z [zespołem pomocy technicznej klienta ustalania muzyki,](https://rakurakuseisan.jp/) aby dodać użytkowników na platformie Ustalania muzyki. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Rozliczanie muzyki w Panelu dostępu należy automatycznie zalogować się do muzyki rozliczania, dla której skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)