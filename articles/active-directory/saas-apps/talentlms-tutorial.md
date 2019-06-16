---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą TalentLMS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0243a3e0ed83abc1edead5ecece4fd5c6ff1cad9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089163"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą TalentLMS

W tym samouczku dowiesz się, jak zintegrować TalentLMS w usłudze Azure Active Directory (Azure AD).
Integrowanie TalentLMS z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do TalentLMS.
* Aby umożliwić użytkownikom można automatycznie zalogowany do TalentLMS (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TalentLMS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* TalentLMS pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje TalentLMS **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-talentlms-from-the-gallery"></a>Dodawanie TalentLMS z galerii

Aby skonfigurować integrację TalentLMS w usłudze Azure AD, należy dodać TalentLMS z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TalentLMS z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **TalentLMS**, wybierz opcję **TalentLMS** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TalentLMS na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą TalentLMS w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TalentLMS musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TalentLMS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie TalentLMS logowania jednokrotnego](#configure-talentlms-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego TalentLMS](#create-talentlms-test-user)**  — aby odpowiednikiem Britta Simon w TalentLMS połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z TalentLMS, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **TalentLMS** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![TalentLMS domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.TalentLMSapp.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta TalentLMS](https://www.talentlms.com/contact) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. Na **Konfigurowanie TalentLMS** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-talentlms-single-sign-on"></a>Konfigurowanie TalentLMS logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie firmy TalentLMS jako administrator.

1. W **konta u & stawienia** kliknij **użytkowników** kartę.

    ![Ustawienia & konta](./media/talentlms-tutorial/IC777296.png "& Ustawienia konta")

1. Kliknij przycisk **logowanie jednokrotne (SSO)** ,

1. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/talentlms-tutorial/IC777297.png "Logowanie jednokrotne")

    a. Z **typu integracja logowania jednokrotnego** listy wybierz **SAML 2.0**.

    b. W **dostawcy tożsamości (IDP)** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal.

    c. Wklej **odcisk palca** wartości z witryny Azure portal do **odcisk palca certyfikatu** pola tekstowego.

    d.  W **zdalnego logowania adresu URL** pola tekstowego, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    e. W **zdalny adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    f. Wypełnij następujące informacje:

    * W **TargetedID** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * W **imię** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * W **nazwisko** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * W **E-mail** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Kliknij pozycję **Zapisz**.

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do TalentLMS.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **TalentLMS**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **TalentLMS**.

    ![Link TalentLMS na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-talentlms-test-user"></a>Tworzenie użytkownika testowego TalentLMS

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do TalentLMS, musi być obsługiwana w TalentLMS. W przypadku TalentLMS Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **TalentLMS** dzierżawy.

1. Kliknij przycisk **użytkowników**, a następnie kliknij przycisk **Dodaj użytkownika**.

1. Na **Dodaj użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie użytkownika](./media/talentlms-tutorial/IC777299.png "Dodawanie użytkownika")  

    a. W polu tekstowym **First name (Imię)** wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.
 
    c. W **adres E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak `brittasimon\@contoso.com`.

    d. Kliknij przycisk **Dodaj użytkownika**.

> [!NOTE]
> Można użyć jakichkolwiek innych TalentLMS użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez TalentLMS do aprowizacji kont użytkowników usługi AAD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka TalentLMS w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze TalentLMS, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

