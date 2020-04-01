---
title: 'Samouczek: Integracja usługi Azure Active Directory z screensteps | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a screensteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091663"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Samouczek: Integracja usługi Azure Active Directory z krokami ekranu

W tym samouczku dowiesz się, jak zintegrować screensteps z usługą Azure Active Directory (Azure AD).
Integracja screensteps z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do ScreenSteps.
* Można włączyć użytkowników, aby automatycznie zalogować się do ScreenSteps (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z screensteps, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego ScreenSteps

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* ScreenSteps obsługuje sso inicjowane przez **usługę SP**

## <a name="adding-screensteps-from-the-gallery"></a>Dodawanie screenstepów z galerii

Aby skonfigurować integrację ScreenSteps do usługi Azure AD, należy dodać ScreenSteps z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać screensteps z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ScreenSteps**, wybierz **ScreenSteps** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Zrzuty ekranu na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z ScreenSteps na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w screensteps.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą screensteps, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne ScreenSteps](#configure-screensteps-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego ScreenSteps](#create-screensteps-test-user)** — aby mieć odpowiednik Britta Simon w ScreenSteps, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą screensteps, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **ScreenSteps** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie ScreenSteps i adresach URL z logami jednokrotnymi](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL logowania, który został wyjaśniony w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie kroków ekranowych** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-screensteps-single-sign-on"></a>Konfigurowanie logowania jednokrotnego screensteps

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy ScreenSteps jako administrator.

1. Kliknij **pozycję Ustawienia konta**.

    ![Zarządzanie kontami](./media/screensteps-tutorial/ic778523.png "Zarządzanie kontami")

1. Kliknij **pozycję Logowanie jednokrotne**.

    ![Uwierzytelnianie zdalne](./media/screensteps-tutorial/ic778524.png "Uwierzytelnianie zdalne")

1. Kliknij **pozycję Utwórz punkt końcowy logowania jednokrotnego**.

    ![Uwierzytelnianie zdalne](./media/screensteps-tutorial/ic778525.png "Uwierzytelnianie zdalne")

1. W sekcji **Tworzenie punktu końcowego logowania jednokrotnego** wykonaj następujące czynności:

    ![Tworzenie punktu końcowego uwierzytelniania](./media/screensteps-tutorial/ic778526.png "Tworzenie punktu końcowego uwierzytelniania")

    a. W polu tekstowym **Tytuł** wpisz tytuł.

    b. Z listy **Tryb** wybierz **SAML**.

    d. Kliknij przycisk **Utwórz**.

1. **Edytuj** nowy punkt końcowy.

    ![Edytowanie punktu końcowego](./media/screensteps-tutorial/ic778528.png "Edytowanie punktu końcowego")

1. W sekcji **Edytuj punkt końcowy logowania jednokrotnego** wykonaj następujące czynności:

    ![Punkt końcowy uwierzytelniania zdalnego](./media/screensteps-tutorial/ic778527.png "Punkt końcowy uwierzytelniania zdalnego")

    a. Kliknij **pozycję Przekaż nowy plik certyfikatu SAML**, a następnie przekaż certyfikat, który został pobrany z witryny Azure portal.

    b. Wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal do pola tekstowego **adresu URL zdalnego logowania.**

    d. Wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure portal do pola tekstowego **Wyloguj adres URL.**

    d. Wybierz **grupę,** do których chcesz przypisać użytkowników podczas ich inicjowania obsługi administracyjnej.

    e. Kliknij przycisk **Update** (Aktualizuj).

    f. Skopiuj **adres URL konsumenta SAML** do schowka i wklej do pola tekstowego **Adresu URL logowania** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    g. Wróć do **punktu końcowego edytowania logowania jednokrotnego**.

    h. Kliknij przycisk **Ustaw domyślnie dla konta,** aby użyć tego punktu końcowego dla wszystkich użytkowników, którzy logują się do ScreenSteps. Alternatywnie można kliknąć przycisk **Dodaj do witryny,** aby użyć tego punktu końcowego dla określonych witryn w **ScreenSteps**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do ScreenSteps.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **pozycję ScreenSteps**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję ScreenSteps**.

    ![Łącze ScreenSteps na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-screensteps-test-user"></a>Utwórz użytkownika testowego ScreenSteps

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w ScreenSteps. Praca z  [zespołem pomocy technicznej klienta ScreenSteps,](https://www.screensteps.com/contact)aby dodać użytkowników na platformie ScreenSteps. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ScreenSteps w Panelu dostępu należy automatycznie zalogować się do screensteps, dla których skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)