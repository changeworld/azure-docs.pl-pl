---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Mixpanel | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: 7fa4c78269464b1a38507acd6512e5f44ed94945
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267671"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Mixpanel

W tym samouczku dowiesz się, jak zintegrować Mixpanel w usłudze Azure Active Directory (Azure AD).
Integrowanie Mixpanel z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Mixpanel.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Mixpanel (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Mixpanel, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Mixpanel logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Mixpanel **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-mixpanel-from-the-gallery"></a>Dodawanie Mixpanel z galerii

Aby skonfigurować integrację Mixpanel w usłudze Azure AD, należy dodać Mixpanel z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Mixpanel z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Mixpanel**, wybierz opcję **Mixpanel** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Mixpanel na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Mixpanel w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika na platformie Mixpanel musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Mixpanel, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Mixpanel logowania jednokrotnego](#configure-mixpanel-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Mixpanel](#create-mixpanel-test-user)**  — aby odpowiednikiem Britta Simon platformie Mixpanel połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Mixpanel, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Mixpanel** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Mixpanel domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://mixpanel.com/login/`

    > [!NOTE]
    > Zarejestruj w [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) skonfigurować poświadczenia logowania i skontaktuj się z pomocą [zespołem pomocy technicznej Mixpanel](mailto:support@mixpanel.com) umożliwiające ustawień logowania jednokrotnego dla Twojej dzierżawy. Możesz również uzyskać swoją wartość na adres URL logowania w razie potrzeby z zespołem pomocy technicznej Mixpanel. 

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Mixpanel** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-mixpanel-single-sign-on"></a>Konfigurowanie Mixpanel logowanie jednokrotne

1. W oknie innej przeglądarki logowanie jednokrotne do aplikacji Mixpanel jako administrator.

2. W dolnej części strony, kliknij polecenie małym **koło zębate** ikony w lewym rogu. 
   
    ![Mixpanel logowanie jednokrotne](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Kliknij przycisk **dostęp zabezpieczeń** kartę, a następnie kliknij przycisk **zmiany ustawień**.
   
    ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Na **zmienić certyfikat** strony okna dialogowego kliknij **Choose file** przekazać certyfikat pobrany, a następnie kliknij przycisk **dalej**.
   
    ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  W polu tekstowym adres URL uwierzytelniania na **zmienić adres URL uwierzytelniania** okna dialogowego strony, Wklej wartość **adres URL logowania** który skopiowano z witryny Azure portal, a następnie kliknij przycisk **dalej**.
   
    ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Kliknij przycisk **Gotowe**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Mixpanel.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Mixpanel**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Mixpanel**.

    ![Link Mixpanel na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-mixpanel-test-user"></a>Tworzenie użytkownika testowego Mixpanel

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon platformie Mixpanel. 

1. Zaloguj się do witryny firmy Mixpanel jako administrator.

2. W dolnej części strony kliknij mały przycisk z kołem zębatym w lewym rogu, aby otworzyć **ustawienia** okna.

3. Kliknij przycisk **zespołu** kartę.

4. W **członek zespołu** pole tekstowe, wpisz adres e-mail Britty na platformie Azure.
   
    ![Ustawienia Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Kliknij pozycję **Invite** (Zaproś). 

> [!Note]
> Użytkownik otrzyma wiadomość e-mail, aby skonfigurować profil.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mixpanel w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Mixpanel, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

