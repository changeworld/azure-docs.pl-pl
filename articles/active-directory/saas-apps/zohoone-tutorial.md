---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą jednego Zoho | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Zoho jeden.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086233"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą jednego Zoho

W tym samouczku dowiesz się, jak zintegrować Zoho jednego z usługą Azure Active Directory (Azure AD).
Integrowanie Zoho jednego z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do jednego Zoho.
* Użytkownikom można automatycznie zalogowany do jednej Zoho (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą jednego Zoho, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Zoho jeden logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje jeden Zoho **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-zoho-one-from-the-gallery"></a>Dodawanie Zoho jednego z galerii

Aby skonfigurować integrację z jednego Zoho w usłudze Azure AD, należy dodać jeden Zoho z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać jeden Zoho z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zoho jeden**, wybierz opcję **Zoho jeden** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Zoho jeden na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą jednego Zoho w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w jednym Zoho musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą jednego Zoho, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Zoho jeden logowania jednokrotnego](#configure-zoho-one-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Zoho jeden](#create-zoho-one-test-user)**  — aby odpowiednikiem Britta Simon w Zoho jednego połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą jednego Zoho, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Zoho jeden** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Adresy URL i jednej domenie Zoho pojedynczego logowania jednokrotnego informacji](common/idp-relay.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `one.zoho.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Poprzedni **adres URL odpowiedzi** wartość nie jest prawdziwe. Zostanie wyświetlony `<saml-identifier>` Wykorzystaj #step4 z **skonfigurować Zoho jeden logowania jednokrotnego** sekcję, co zostało wyjaśnione w dalszej części tego samouczka.

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `https://one.zoho.com`

5. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, wykonaj następujące kroki:


    ![Adresy URL i jednej domenie Zoho pojedynczego logowania jednokrotnego informacji](common/both-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Poprzedni **adres URL logowania** wartość nie jest prawdziwe. Wartość zostaną zaktualizowane o rzeczywisty adres URL logowania z **skonfigurować Zoho jeden logowania jednokrotnego** sekcję, co zostało wyjaśnione w dalszej części tego samouczka. 

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

7. Na **skonfigurować jeden Zoho** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-zoho-one-single-sign-on"></a>Skonfiguruj jeden Zoho logowania jednokrotnego

1. W oknie przeglądarki internetowej innej Zaloguj się w jednym Zoho witryny firmy jako administrator.

2. Na **organizacji** kartę, kliknij przycisk **instalacji** w obszarze **uwierzytelnianie SAML**.

    ![Zoho jednej organizacji](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. W oknie podręcznym strony, wykonaj następujące czynności:

    ![Zoho One sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. W **adres URL logowania** pola tekstowego, Wklej wartość **adres URL logowania**, który skopiowano z witryny Azure portal.

    b. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    c. Kliknij przycisk **Przeglądaj** do przekazania **certyfikat (Base64)** który został pobrany z witryny Azure portal.

    d. Kliknij pozycję **Zapisz**.

4. Po zapisaniu ustawień uwierzytelniania SAML, skopiuj **identyfikator SAML** wartości i Dołącz do niej **adres URL odpowiedzi** zamiast `<saml-identifier>`, takiej jak `https://accounts.zoho.com/samlresponse/one.zoho.com` i Wklej wartość wygenerowanego w **Adres URL odpowiedzi** polu tekstowym w obszarze **podstawową konfigurację protokołu SAML** sekcji.

    ![Zoho jednego języka saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Przejdź do **domen** kartę, a następnie kliknij przycisk **dodawania domeny**.

    ![Zoho jedną domenę](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na **dodawania domeny** strony, wykonaj następujące czynności:

    ![Dodawanie Zoho jednej domeny](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. W **nazwy domeny** pola tekstowego, typ domeny, np. contoso.com.

    b. Kliknij pozycję **Add** (Dodaj).

    >[!Note]
    >Po dodaniu domeny postępuj zgodnie z [te](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroki, aby zweryfikować domenę. Po zweryfikowaniu domeny, należy użyć nazwy domeny w **adres URL logowania** w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do jednego Zoho.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Zoho jeden**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Zoho jeden**.

    ![Zoho jednego linku na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zoho-one-test-user"></a>Utwórz jedną Zoho użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD zarejestrować się w jednym Zoho, musi być obsługiwana w jednym Zoho. W pierwszej Zoho aprowizacji to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Zoho jeden jako Administrator zabezpieczeń.

2. Na **użytkowników** karcie, kliknij przycisk **logo użytkownika**.

    ![Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na **Dodaj użytkownika** strony, wykonaj następujące czynności:

    ![Dodaj Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. W **nazwa** tekstu wprowadź nazwę użytkownika, takich jak **Britta simon**.
    
    b. W **adres E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak brittasimon@contoso.com.

    >[!Note]
    >Wybierz zweryfikowaną domenę z listy domen.

    c. Kliknij pozycję **Add** (Dodaj).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu jednego Zoho kafelka w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze dla którego skonfigurować logowanie Jednokrotne jeden Zoho. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

