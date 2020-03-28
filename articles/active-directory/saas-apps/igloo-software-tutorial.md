---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Igloo | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a oprogramowaniem Igloo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100580"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Igloo

W tym samouczku dowiesz się, jak zintegrować oprogramowanie Igloo z usługą Azure Active Directory (Azure AD).
Integracja oprogramowania Igloo z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do oprogramowania Igloo.
* Można włączyć użytkowników do automatycznego logowania się do oprogramowania Igloo (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem Igloo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Igloo Software

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Igloo Software obsługuje zainicjowane przez **SP** SSO
* Oprogramowanie Igloo obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-igloo-software-from-the-gallery"></a>Dodawanie oprogramowania Igloo z galerii

Aby skonfigurować integrację oprogramowania Igloo z usługą Azure AD, należy dodać oprogramowanie Igloo z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie Igloo z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Igloo Software**, wybierz **Igloo Software** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Igloo Software na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą oprogramowania Igloo na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w oprogramowaniu Igloo.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą oprogramowania Igloo, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne oprogramowania Igloo](#configure-igloo-software-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego oprogramowania Igloo](#create-igloo-software-test-user)** — aby mieć odpowiednik Britta Simon w oprogramowaniu Igloo, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą oprogramowania Igloo, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Igloo** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie oprogramowania Igloo i adresach URL](common/sp-identifier-reply.png)

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<company name>.igloocommmunities.com`

    b. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<company name>.igloocommmunities.com/saml.digest`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej Igloo Software Client,](https://www.igloosoftware.com/services/support) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie oprogramowania Igloo** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-igloo-software-single-sign-on"></a>Konfigurowanie logowania jednokrotnego oprogramowania Igloo

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Igloo Software jako administrator.

2. Przejdź do **Panelu sterowania**.

     ![Panel sterowania](./media/igloo-software-tutorial/ic799949.png "Panel sterowania")

3. Na karcie **Członkostwo** kliknij pozycję **Ustawienia logowania**.

    ![Ustawienia logowania](./media/igloo-software-tutorial/ic783968.png "Ustawienia logowania")

4. W sekcji Konfiguracja SAML kliknij pozycję **Konfiguruj uwierzytelnianie SAML**.

    ![Konfiguracja SAML](./media/igloo-software-tutorial/ic783969.png "Konfiguracja SAML")

5. W sekcji **Konfiguracja ogólna** wykonaj następujące czynności:

    ![Konfiguracja ogólna](./media/igloo-software-tutorial/ic783970.png "Konfiguracja ogólna")

    a. W **polach tekstowych Nazwa połączenia** wpisz niestandardową nazwę konfiguracji.

    b. W polu tekstowym **adresu URL logowania IdP** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **adresu URL wylogowania IdP** wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure portal.

    d. Wybierz pozycję **Odpowiedź wyloguj i poproś o typ HTTP** jako **POST**.

    e. Otwórz certyfikat zakodowany w **bazie podstawowej 64** w notatniku pobranym z witryny Azure portal, skopiuj jego zawartość do schowka, a następnie wklej ją do pola tekstowego **certyfikatu publicznego.**

6. W konfiguracji **odpowiedzi i uwierzytelniania**wykonaj następujące czynności:

    ![Konfiguracja odpowiedzi i uwierzytelniania](./media/igloo-software-tutorial/IC783971.png "Konfiguracja odpowiedzi i uwierzytelniania")
  
    a. Jako **dostawca tożsamości**wybierz pozycję Microsoft **ADFS**.

    b. Jako **typ identyfikatora**wybierz adres **e-mail**. 

    d. W **polach tekstowych Atrybutu wiadomości e-mail** wpisz adres **e-mail**.

    d. W polach tekstowych **Atrybut imienia** wpisz **nazwę givenname**.

    e. W polach tekstowych **Atrybut nazwisko** wpisz **nazwisko**.

7. Wykonaj następujące czynności, aby ukończyć konfigurację:

    ![Tworzenie użytkownika w logowanie](./media/igloo-software-tutorial/IC783972.png "Tworzenie użytkownika w logowanie") 

    a. Podczas **tworzenia użytkownika podczas logowania**wybierz pozycję **Utwórz nowego użytkownika w witrynie po zalogowaniu się.**

    b. Jako **Ustawienia logowania**wybierz przycisk Użyj **SAML na ekranie "Zaloguj się".**

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
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do oprogramowania Igloo.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Igloo Software**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Igloo Software**.

    ![Łącze Igloo Software na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-igloo-software-test-user"></a>Tworzenie użytkownika testowego oprogramowania Igloo

Nie ma żadnego elementu akcji, aby skonfigurować inicjowanie obsługi administracyjnej użytkownika do oprogramowania Igloo.  

Gdy przypisany użytkownik próbuje zalogować się do Igloo Software za pomocą panelu dostępu, oprogramowanie Igloo sprawdza, czy użytkownik istnieje.  Jeśli nie ma jeszcze dostępnego konta użytkownika, jest ono automatycznie tworzone przez Igloo Software.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Igloo Software w Panelu dostępu należy automatycznie zalogować się do oprogramowania Igloo, dla którego skonfigurowano logującą się do rejestru jednośmiękowego. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)