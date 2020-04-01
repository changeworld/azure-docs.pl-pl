---
title: 'Samouczek: Integracja usługi Azure Active Directory z LearnUpon | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098236"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Samouczek: Integracja usługi Azure Active Directory z learnupon

W tym samouczku dowiesz się, jak zintegrować LearnUpon z usługą Azure Active Directory (Azure AD).
Integracja LearnUpon z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do LearnUpon.
* Można włączyć użytkowników, aby automatycznie zalogować się do LearnUpon (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z LearnUpon, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* LearnUpon subskrypcji z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.


* LearnUpon obsługuje zainicjowane przez **IDP jednostkę** SSO

* LearnUpon obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time


## <a name="adding-learnupon-from-the-gallery"></a>Dodawanie LearnUpon z galerii

Aby skonfigurować integrację LearnUpon do usługi Azure AD, należy dodać LearnUpon z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać LearnUpon z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **LearnUpon**, wybierz **pozycję LearnUpon** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![LearnUpon na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z LearnUpon na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w LearnUpon.

Aby skonfigurować i przetestować usługę Azure AD logowania jednokrotnego za pomocą LearnUpon, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj LearnUpon Log-On](#configure-learnupon-single-sign-on)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz learnupon użytkownika testowego](#create-learnupon-test-user)** - mieć odpowiednik Britta Simon w LearnUpon, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą LearnUpon, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **LearnUpon** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![LearnUpon Domain i adresy URL — informacje o loguchu jednokrotnym](common/idp-reply.png)

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta LearnUpon,](https://www.learnupon.com/features/support/) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą SAML** znajdź **ODCISK PALCA** — zostanie on dodany do ustawień SAML LearnUpon.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfigurowanie LearnUpon** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-learnupon-single-sign-on"></a>Konfigurowanie learnupon logowanie jednokrotne

1. Otwórz inne wystąpienie przeglądarki i zaloguj się do LearnUpon przy za pomocą konta administratora.

1. Kliknij kartę **Ustawienia.**

    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Kliknij **pozycję Jednokrotne zalogowanie — SAML**, a następnie kliknij pozycję **Ustawienia ogólne,** aby skonfigurować ustawienia SAML.
   
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. W sekcji **Ustawienia ogólne** wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Wybierz pozycję **Włączone**.

    b. Wybierz **wersję** jako **2.0**.

    d. Wybierz **pozycję Pomiń warunki** jako **nie**.

    d. W pole tekstowym **nazwa param tokenu SAML** wpisz nazwę parametru post żądania na wskazanym powyżej adresie URL konsumenta SAML, który zawiera aserację SAML, która ma zostać zweryfikowana i uwierzytelniona — na przykład **SAMLResponse**.

    e. W polu **tekstowym Format identyfikatora nazwy** wpisz wartość wskazującą miejsce, w którym znajduje się identyfikator użytkownika `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`(adres e-mail) w asercji SAML — na przykład .
  
    f. W polu tekstowym **Identyfikowanie lokalizacji dostawcy** wpisz wartość, która wskazuje, do której użytkownicy są wysyłani, jeśli klikną ikonę przesłane z ekranu logowania usługi Azure portal.
  
    g. W polu tekstowym **Wyloguj adres URL** wklej wartość **adresu URL wylogowania,** która została skopiowana z witryny Azure portal.

    h. Kliknij pozycję **Zarządzaj odbitkami palcami,** a następnie przekaż odcisk palca pobranego certyfikatu.

1. Kliknij **pozycję Ustawienia użytkownika**, a następnie wykonaj następujące czynności:

     ![Konfigurowanie logowania jednokrotnego](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. W polu tekstowym **Format identyfikatora** imienia wpisz wartość, która informuje nas, gdzie w asercji `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`SAML znajduje się nazwa użytkownika - na przykład: .
  
    b. W polu tekstowym **Format identyfikatora nazwiska** wpisz wartość, która informuje nas, gdzie w asercji `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`SAML znajduje się ostatnianazwa użytkowników - na przykład: .

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do LearnUpon.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **LearnUpon**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **learnupon**.

    ![Łącze LearnUpon na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-learnupon-test-user"></a>Utwórz użytkownika testowego LearnUpon

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w LearnUpon. LearnUpon obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w LearnUpon, nowy jest tworzony po uwierzytelnieniu. Jeśli chcesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka LearnUpon w Panelu dostępu należy automatycznie zalogować się do learnUpon, dla którego skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)