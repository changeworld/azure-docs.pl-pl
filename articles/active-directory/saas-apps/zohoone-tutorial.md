---
title: 'Samouczek: Integracja usługi Azure Active Directory z Zoho One | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086233"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Samouczek: Integracja usługi Azure Active Directory z Zoho One

W tym samouczku dowiesz się, jak zintegrować Zoho One z usługą Azure Active Directory (Azure AD).
Integracja Zoho One z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Zoho One.
* Można włączyć użytkowników, aby automatycznie zalogować się do Zoho One (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Zoho One, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Zoho Jedna subskrypcja z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zoho One obsługuje **sp** i **IDP** zainicjowane SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Dodawanie Zoho One z galerii

Aby skonfigurować integrację Zoho One w usłudze Azure AD, należy dodać Zoho One z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Zoho One z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zoho One**, wybierz **Zoho One** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Zoho One na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Zoho One na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Zoho One.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Zoho One, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Zoho One Single Sign-On](#configure-zoho-one-single-sign-on)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Zoho One](#create-zoho-one-test-user)** — aby mieć odpowiednik Britta Simon w Zoho One, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi Zoho One, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Zoho One** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Informacje o logach jednorazowych z o.o.](common/idp-relay.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL:`one.zoho.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Poprzednia wartość **adresu URL odpowiedzi** nie jest rzeczywista. Otrzymasz `<saml-identifier>` wartość z #step4 **zfiguruj sekcję Zoho One Single Sign-On** , która została wyjaśniona w dalszej części samouczka.

    d. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `https://one.zoho.com`

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **sp,** wykonaj następujący krok:


    ![Informacje o logach jednorazowych z o.o.](common/both-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Poprzednia wartość **adresu URL logowania** nie jest rzeczywista. Wartość zostanie zaktualizowana za pomocą rzeczywistego adresu URL logowania z sekcji **Configure Zoho One Single Sign-On,** która została wyjaśniona w dalszej części samouczka. 

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie Zoho One** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Zoho

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Zoho One jako administrator.

2. Na karcie **Organizacja** kliknij pozycję **Ustawienia** w obszarze **Uwierzytelnianie SAML**.

    ![Zoho Jeden org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na stronie podręcznej wykonaj następujące czynności:

    ![Zoho Jeden ig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. W polu tekstowym **Adresu URL logowania wklej** wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    b. W polu tekstowym **adresu URL wylogowywania** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure Portal.

    d. Kliknij **przycisk Przeglądaj,** aby przekazać **certyfikat (Base64),** który został pobrany z witryny Azure portal.

    d. Kliknij przycisk **Zapisz**.

4. Po zapisaniu konfiguracji uwierzytelniania SAML skopiuj wartość **IDENTYFIKATORA SAML** `<saml-identifier>`i `https://accounts.zoho.com/samlresponse/one.zoho.com` dołącz ją do adresu **URL odpowiedzi** zamiast , polub i wklej wygenerowaną wartość w polu tekstowym **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML.**

    ![Zoho Jeden saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Przejdź do karty **Domeny,** a następnie kliknij pozycję **Dodaj domenę**.

    ![Domena Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na stronie **Dodawanie domeny** wykonaj następujące czynności:

    ![Zoho Jedna domena dodawania](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. W polu tekstowym **Nazwa domeny** wpisz domenę, taką jak contoso.com.

    b. Kliknij przycisk **Dodaj**.

    >[!Note]
    >Po dodaniu domeny wykonaj [następujące](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroki, aby zweryfikować domenę. Po zweryfikowaniu domeny użyj nazwy domeny w **adresie URL logowania** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do Zoho One.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Zoho One**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Zoho One**.

    ![Łącze Zoho One na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zoho-one-test-user"></a>Tworzenie użytkownika testowego Zoho One

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Zoho One, muszą one być aprowizować do Zoho One. W Zoho One inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Zoho One jako administrator zabezpieczeń.

2. Na karcie **Użytkownicy** kliknij **logo użytkownika**.

    ![Zoho Jeden użytkownik](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zoho Jeden dodać użytkownika](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. W polu tekstowym **Nazwa** wprowadź nazwę użytkownika, takiego jak **Britta simon**.
    
    b. W polu tekstowym **Email Address** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład brittasimon@contoso.com.

    >[!Note]
    >Wybierz zweryfikowaną domenę z listy domen.

    d. Kliknij przycisk **Dodaj**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Zoho One w Panelu dostępu należy automatycznie zalogować się do Zoho One, dla którego skonfigurowano logującą się logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

