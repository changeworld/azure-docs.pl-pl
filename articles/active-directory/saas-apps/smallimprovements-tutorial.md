---
title: 'Samouczek: Integracja usługi Azure Active Directory z małymi ulepszeniami | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a małymi ulepszeniami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: d2d0bbc7a6e1c680434041d1b9d55e39a96b6f44
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090374"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Samouczek: Integracja usługi Azure Active Directory z małymi ulepszeniami

W tym samouczku dowiesz się, jak zintegrować małe ulepszenia z usługą Azure Active Directory (Azure AD).
Integracja małych ulepszeń z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do małych ulepszeń.
* Można włączyć użytkowników, aby automatycznie zalogować się do małych ulepszeń (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z małymi ulepszeniami, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego small improvements

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Małe ulepszenia obsługuje **SP** zainicjowane SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Dodawanie drobnych ulepszeń z galerii

Aby skonfigurować integrację małych ulepszeń w usłudze Azure AD, należy dodać małe ulepszenia z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać małe ulepszenia z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **małe ulepszenia**, wybierz pozycję **Małe ulepszenia** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Małe ulepszenia na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z małych ulepszeń na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w obszarze Małe ulepszenia.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą małych ulepszeń, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj małe ulepszenia Logowanie jednokrotne](#configure-small-improvements-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz mały użytkownik testowy ulepszeń](#create-small-improvements-test-user)** — aby mieć odpowiednik Britta Simon w małych ulepszeń, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą małych ulepszeń, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Small Improvements** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Małe ulepszenia Domeny i adresy URL — informacje o logowanie jednokrotnym](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.small-improvements.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta small improvements,](mailto:support@small-improvements.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie małych ulepszeń** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-small-improvements-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w małych ulepszeniach

1. W innym oknie przeglądarki zaloguj się do witryny firmy Small Improvements jako administrator.

1. Na głównej stronie pulpitu nawigacyjnego kliknij przycisk **Administracja** po lewej stronie.

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Kliknij przycisk **JEDNOTŁk SAML** z sekcji **Integracje.**

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na stronie Ustawienia samego systemu syłk wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. W polu tekstowym **punktu końcowego HTTP** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    b. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej go do pola tekstowego **certyfikatu x509.** 

    d. Jeśli chcesz mieć opcję uwierzytelniania logowania i logowania dla użytkowników, sprawdź opcję **Włącz dostęp za pomocą loginu / hasła.**  

    d. Enter the appropriate value to Name the SSO Login button in the **SAML Prompt** textbox.  

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
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do małych ulepszeń.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję Małe ulepszenia**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Małe ulepszenia**.

    ![Łącze Małe ulepszenia na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-small-improvements-test-user"></a>Utwórz mały użytkownik testowy ulepszeń

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do małych ulepszeń, muszą one być aprowiowane w małych ulepszeń. W przypadku małych ulepszeń inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Small Improvements jako administrator.

1. Na stronie głównej przejdź do menu po lewej stronie, kliknij pozycję **Administracja**.

1. Kliknij przycisk **Katalog użytkowników** w sekcji Zarządzanie użytkownikami.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Kliknij **pozycję Dodaj użytkowników**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. W oknie dialogowym **Dodawanie użytkowników** wykonaj następujące czynności: 

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Wprowadź **imię** użytkownika, takiego jak **Britta**.

    b. Wprowadź **nazwisko** użytkownika, takiego jak **Simon**.

    d. Wprowadź **adres e-mail** użytkownika w stylu **brittasimon@contoso.com**.

    d. Możesz również wprowadzić wiadomość osobistą w polu **Wyślij powiadomienie e-mail.** Jeśli nie chcesz wysyłać powiadomienia, wyewidencjonuj to pole wyboru.

    e. Kliknij **pozycję Utwórz użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Małe ulepszenia w Panelu dostępu należy automatycznie zalogować się do małego ulepszenia, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)