---
title: 'Samouczek: Integracja usługi Azure Active Directory z Sauce Labs — testowanie na urządzeniach mobilnych i w sieci Web | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a sauce labs — mobile i web testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8933cb90672e49305cd0fb7dc5e4f8f04f94093e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091557"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Samouczek: Integracja usługi Azure Active Directory z laboratoriami Sauce Labs — testowanie na urządzeniach mobilnych i w sieci Web

W tym samouczku dowiesz się, jak zintegrować Sauce Labs — Mobile i testowanie sieci Web z usługą Azure Active Directory (Azure AD).
Integracja Sauce Labs — testowanie mobilne i internetowe z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do Sauce Labs — mobile i web testing.
* Można włączyć użytkowników, aby automatycznie zalogować się do Sauce Labs — mobile i web testing (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sauce labs — mobile i web testing, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Sauce Labs - Subskrypcja z obsługą logowania jednokrotnego i testowania sieci Web

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Sauce Labs - Testy mobilne i internetowe obsługują zainicjowane przez **IDP** SSO
* Sauce Labs - Testowanie mobilne i internetowe obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Dodawanie Sauce Labs - Mobile and Web Testing z galerii

Aby skonfigurować integrację Sauce Labs — Mobile and Web Testing w usłudze Azure AD, należy dodać Sauce Labs — Mobile i Web Testing z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Sauce Labs - Mobile i Web Testing z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Sauce Labs - Mobile and Web Testing**, wybierz Sauce **Labs - Mobile and Web Testing** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Sauce Labs - Testy mobilne i internetowe na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Sauce Labs - Mobile i Web Testing na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sauce labs — mobile i web testing.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą sauce labs — mobile i web testing, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Sauce Labs — mobile and Web Testing Single Sign-On](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz Sauce Labs — mobile i web testing test użytkownika](#create-sauce-labs---mobile-and-web-testing-test-user)** — aby mieć odpowiednik Britta Simon w Sauce Labs — mobile i web testing, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą sauce labs — mobile i web testing, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie **Sauce Labs — Mobile and Web Testing** application integration page, select Single **sign-on (Rejestracja jednokrotna).**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Sauce Labs - Mobile i Web Testing Domain i adresy URL — informacje o logach jednokrotnych](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie laboratoriów sauce – mobile i web testing** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurowanie Sauce Labs - Mobile i Web Testing Single Sign-On

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Sauce Labs — Mobile i Web Testing jako administrator.

2. Kliknij **ikonę Użytkownik** i wybierz kartę **Zarządzanie zespołem.**

    ![Konfigurowanie logowania jednokrotnego](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Wprowadź **nazwę domeny** w obszarze tekstowym.

    ![Konfigurowanie logowania jednokrotnego](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Kliknij pozycję **Konfiguruj** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. W sekcji **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności.

    ![Konfigurowanie logowania jednokrotnego](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Kliknij **przycisk Przeglądaj** i przekaż pobrany plik metadanych z usługi Azure AD.

    b. Zaznacz pole wyboru **ZEZWALAJ NA INICJOWANIE OBSŁUGI ADMINISTRACYJNEJ TYLKO W CZASIE.**

    d. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do Sauce Labs — mobile i testowania sieci Web.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję Sauce Labs — Mobile and Web Testing**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **opcję Sauce Labs - Mobile and Web Testing**.

    ![Sauce Labs - Łącze do testowania urządzeń mobilnych i stron internetowych na liście Aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Tworzenie Sauce Labs - Użytkownik testowy testów mobilnych i internetowych

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Sauce Labs - Mobile i Web Testing. Sauce Labs — testy mobilne i internetowe obsługują inicjowanie obsługi administracyjnej użytkowników tylko w czasie, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Sauce Labs — Mobile i Web Testing, nowy jest tworzony po uwierzytelnieniu.

> [!Note]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Sauce Labs — Mobile and Web Testing w Panelu dostępu należy automatycznie zalogować się do sauce labs — mobile i web testing, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

