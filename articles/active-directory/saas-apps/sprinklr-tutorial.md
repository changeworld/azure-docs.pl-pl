---
title: 'Samouczek: Integracja usługi Azure Active Directory ze sprinklr | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089660"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Samouczek: Integracja usługi Azure Active Directory ze sprinklr

W tym samouczku dowiesz się, jak zintegrować sprinklr z usługą Azure Active Directory (Azure AD).
Integracja sprinklr z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Sprinklr.
* Można włączyć użytkowników, aby automatycznie zalogować się do Sprinklr (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD ze sprinklr, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Sprinklr

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Sprinklr **obsługuje** sp zainicjowane SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Dodawanie Sprinklr z galerii

Aby skonfigurować integrację sprinklr z usługą Azure AD, należy dodać Sprinklr z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Sprinklr z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Sprinklr**, wybierz **Opcję Sprinklr** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Sprinklr na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Sprinklr na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sprinklr.

Aby skonfigurować i przetestować usługę Azure AD jednokrotnego logowania za pomocą Sprinklr, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj sprinklr logowanie jednokrotne](#configure-sprinklr-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Sprinklr](#create-sprinklr-test-user)** — aby mieć odpowiednik Britta Simon w Sprinklr, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą sprinklr, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Sprinklr** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie sprinklr i adresach URL z logowaniami jednokrotnymi](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.sprinklr.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Sprinklr,](https://www.sprinklr.com/contact-us/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie sprinklr** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej sprinklr

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Sprinklr jako administrator.

1. Przejdź do **ustawień administracyjnych \> **.

    ![Administracja](./media/sprinklr-tutorial/ic782907.png "Administracja")

1. Przejdź do **strony Zarządzanie logiem jednokrotnym partnera \> ** w lewym okienku.

    ![Zarządzanie partnerem](./media/sprinklr-tutorial/ic782908.png "Zarządzanie partnerem")

1. Kliknij **pozycję +Dodaj znaki jednokrotne**.

    ![Logowanie jednokrotne](./media/sprinklr-tutorial/ic782909.png "Logowanie jednokrotne")

1. Na stronie **Znak jednokrotny na** stronie wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/sprinklr-tutorial/ic782910.png "Logowanie jednokrotne")

    a. W polach tekstowych **Nazwa** wpisz nazwę konfiguracji (na przykład: *WAADSSOTest*).

    b. Wybierz pozycję **Włączone**.

    d. Wybierz **pozycję Użyj nowego certyfikatu SSO**.

    d. Otwórz certyfikat zakodowany w bazie podstawowej-64 w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **Certyfikat dostawcy tożsamości.**

    e. Wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal do pola tekstowego **identyfikatora jednostki.**

    f. Wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure Portal do pola tekstowego **adresu URL logowania dostawcy tożsamości.**

    g. Wklej wartość **adresu URL wylogowania** skopiowaną z witryny Azure Portal do pola tekstowego **URL wylogowania dostawcy tożsamości.**

    h. Jako **typ identyfikatora użytkownika SAML,** wybierz **twierdzenie zawiera sprinklr.com nazwę użytkownika**.

    i. Jako **lokalizacja identyfikatora użytkownika SAML**wybierz identyfikator **użytkownika w elemencie identyfikatora nazwy instrukcji Podmiot**.

    j. Kliknij przycisk **Zapisz**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do Sprinklr.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz opcję **Sprinklr**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz opcję **Sprinklr**.

    ![Łącze Sprinklr na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sprinklr-test-user"></a>Utwórz użytkownika testowego Sprinklr

1. Zaloguj się do witryny firmy Sprinklr jako administrator.

1. Przejdź do **ustawień administracyjnych \> **.

    ![Administracja](./media/sprinklr-tutorial/ic782907.png "Administracja")

1. Przejdź do **strony Zarządzanie użytkownikami klienta \> ** z lewego okienka.

    ![Ustawienia](./media/sprinklr-tutorial/ic782914.png "Ustawienia")

1. Kliknij **pozycję Dodaj użytkownika**.

    ![Ustawienia](./media/sprinklr-tutorial/ic782915.png "Ustawienia")

1. W oknie dialogowym **Edytowanie użytkownika** wykonaj następujące czynności:

    ![Edytuj użytkownika](./media/sprinklr-tutorial/ic782916.png "Edytuj użytkownika")

    a. W polach **tekstowych Poczta e-mail**i **imię** i **nazwisko** wpisz informacje o koncie użytkownika usługi Azure AD, które chcesz aprowizować.

    b. Wybierz **hasło wyłączone**.

    d. Wybierz **język**.

    d. Wybierz **typ użytkownika**.

    e. Kliknij przycisk **Update** (Aktualizuj).

    > [!IMPORTANT]
    > **Hasło wyłączone** musi być wybrane, aby umożliwić użytkownikowi zalogowanie się za pośrednictwem dostawcy tożsamości. 

1. Przejdź do **roli**, a następnie wykonaj następujące kroki:

    ![Role partnerów](./media/sprinklr-tutorial/ic782917.png "Role partnerów")

    a. Z listy **Globalne** wybierz **ALL_Permissions**.  

    b. Kliknij przycisk **Update** (Aktualizuj).

> [!NOTE]
> Można użyć innych narzędzi do tworzenia konta użytkownika Sprinklr lub interfejsów API dostarczonych przez Sprinklr do aprowizowania kont użytkowników usługi Azure AD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Sprinklr w Panelu dostępu należy automatycznie zalogować się do sprinklr, dla którego skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
