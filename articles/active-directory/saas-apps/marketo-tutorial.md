---
title: 'Samouczek: Integracja usługi Azure Active Directory z Marketo | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0488fd1e9bc10d61d6660745acfc8c39becf3a89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159461"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Samouczek: Integracja usługi Azure Active Directory z Marketo

W tym samouczku dowiesz się, jak zintegrować Marketo z usługą Azure Active Directory (Azure AD).
Integracja Marketo z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Marketo.
* Można włączyć użytkowników, aby automatycznie zalogować się do Marketo (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z marketo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Marketo

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Marketo obsługuje zainicjowane **przez IDP** SSO

## <a name="adding-marketo-from-the-gallery"></a>Dodawanie Marketo z galerii

Aby skonfigurować integrację Marketo z usługą Azure AD, należy dodać Marketo z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Marketo z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Marketo**, wybierz **Marketo** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Marketo na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Marketo na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Marketo.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Marketo, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Marketo](#configure-marketo-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Marketo](#create-marketo-test-user)** — aby mieć odpowiednik Britta Simon w Marketo, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednoobjęte usługi Azure AD za pomocą Marketo, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Marketo** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Informacje o logach jednolitych w domenie Marketo i adresach URL](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://saml.marketo.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Marketo,](https://investors.marketo.com/contactus.cfm) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie Marketo** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-marketo-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Marketo

1. Aby uzyskać identyfikator Munchkina aplikacji, zaloguj się do Marketo przy użyciu poświadczeń administratora i wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **Administrator** w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    d. Przejdź do menu Integracja i kliknij **łącze Munchkin**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Skopiuj identyfikator Munchkina wyświetlany na ekranie i uzupełnij adres URL odpowiedzi w Kreatorze konfiguracji usługi Azure AD.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Aby skonfigurować zgłoszenie sytego w aplikacji, wykonaj poniższe czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **Administrator** w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    d. Przejdź do menu Integracja i kliknij przycisk **Zaloguj się jednokrotnie**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Aby włączyć ustawienia SAML, kliknij przycisk **Edytuj.**
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Włączona** Ustawienia logowania jednokrotnego.
   
    f. Wklej **identyfikator usługi Azure AD**w polach tekstowych **identyfikatora wystawcy.**
   
    g. W polach tekstowych **Identyfikator jednostki** wprowadź adres URL jako `http://saml.marketo.com/sp`.
   
    h. Wybierz element Lokalizacja identyfikatora użytkownika jako **identyfikator nazwy**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Jeśli identyfikator użytkownika nie jest wartością UPN, zmień wartość na karcie Atrybut.
   
    i. Przekaż certyfikat pobrany z kreatora konfiguracji usługi Azure AD. **Zapisz** ustawienia.
   
    j. Edytuj ustawienia przekierowania stron.
   
    k. Wklej **adres URL logowania** w pola tekstowym **Adresu URL logowania.**
   
    l. Wklej **adres URL wylogowania** w pola tekstowym **adresu URL wylogowania.**
   
    m. W **adresie URL błędu**skopiuj **adres URL wystąpienia Marketo** i kliknij przycisk **Zapisz,** aby zapisać ustawienia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Aby włączyć funkcję SSO dla użytkowników, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **Administrator** w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    d. Przejdź do menu **Zabezpieczenia** i kliknij pozycję **Ustawienia logowania**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Sprawdź opcję **Wymagaj logowania przyuszeń** i **zapisz** ustawienia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_14.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **\@brittasimon yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Marketo.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję Marketo**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **marketo**.

    ![Link Marketo na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-marketo-test-user"></a>Tworzenie użytkownika testowego Marketo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Marketo. wykonaj następujące kroki, aby utworzyć użytkownika na platformie Marketo.

1. Zaloguj się do aplikacji Marketo przy użyciu poświadczeń administratora.

2. Kliknij przycisk **Administrator** w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Przejdź do menu **Zabezpieczenia** i kliknij pozycję **Użytkownicy & role**
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kliknij **łącze Zaproś nowego użytkownika** na karcie Użytkownicy
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. W kreatorze Zapraszanie nowego użytkownika wypełnij następujące informacje
   
    a. Wprowadzanie adresu **e-mail** użytkownika w skrzynce tekstowej
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Wprowadź **imię** w skrzynce tekstowej
   
    d. Wprowadź **nazwisko** w skrzynce tekstowej
   
    d. Kliknij **przycisk Dalej**

6. Na karcie **Uprawnienia** wybierz **pozycję UżytkownikaRole** i kliknij przycisk **Dalej**
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknij przycisk **Wyślij,** aby wysłać zaproszenie użytkownika
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Użytkownik otrzymuje powiadomienie e-mail i musi kliknąć link i zmienić hasło, aby aktywować konto. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Marketo w Panelu dostępu należy automatycznie zalogować się do Marketo, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

