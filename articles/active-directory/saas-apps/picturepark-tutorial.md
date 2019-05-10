---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Picturepark | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 617c75024b45dab7ff2466b99bfb71c18cdd778a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230031"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Picturepark

W tym samouczku dowiesz się, jak zintegrować Picturepark w usłudze Azure Active Directory (Azure AD).
Integrowanie Picturepark z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Picturepark.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Picturepark (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Picturepark, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Picturepark logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Picturepark **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-picturepark-from-the-gallery"></a>Dodawanie Picturepark z galerii

Aby skonfigurować integrację Picturepark w usłudze Azure AD, należy dodać Picturepark z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Picturepark z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Picturepark**, wybierz opcję **Picturepark** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Picturepark na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Picturepark w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Picturepark musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Picturepark, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Picturepark logowania jednokrotnego](#configure-picturepark-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Picturepark](#create-picturepark-test-user)**  — aby odpowiednikiem Britta Simon w Picturepark połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Picturepark, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Picturepark** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Picturepark domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.picturepark.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Picturepark](https://picturepark.com/about/contact/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **Odcisk palca** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. Na **Konfigurowanie Picturepark** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami. Aby uzyskać **adres URL logowania**, użyj wartości z następującym wzorcem: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ jest identyfikator dzierżawy subskrypcji usługi Azure AD.

    ![Kopiowanie adresów URL konfiguracji](./media/picturepark-tutorial/configurls.png)

    a. Identyfikator usługi Azure AD

    b. Adres URL wylogowywania

### <a name="configure-picturepark-single-sign-on"></a>Konfigurowanie Picturepark logowanie jednokrotne

1. W oknie przeglądarki innej witryny sieci web należy zalogować się do witryny firmy Picturepark jako administrator.

2. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **konsoli zarządzania**.
   
    ![Konsola zarządzania](./media/picturepark-tutorial/ic795062.png "Konsola zarządzania")

3. Kliknij przycisk **uwierzytelniania**, a następnie kliknij przycisk **dostawców tożsamości**.
   
    ![Uwierzytelnianie](./media/picturepark-tutorial/ic795063.png "Uwierzytelnianie")

4. W **konfiguracji dostawcy tożsamości** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja dostawcy tożsamości](./media/picturepark-tutorial/ic795064.png "konfiguracji dostawcy tożsamości")
   
    a. Kliknij pozycję **Add** (Dodaj).
  
    b. Wpisz nazwę dla danej konfiguracji.
   
    c. Wybierz **Ustaw jako domyślny**.
   
    d. W **URI wystawcy** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.
   
    e. W **zaufanego wystawcy odcisk palca** pola tekstowego, Wklej wartość **odcisk palca** skopiowanej z **certyfikat podpisywania SAML** sekcji. 

5. Kliknij przycisk **JoinDefaultUsersGroup**.

6. Aby ustawić **Emailaddress** atrybutu w **oświadczenia** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` i kliknij przycisk **Zapisz**.

      ![Konfiguracja](./media/picturepark-tutorial/ic795065.png "konfiguracji")

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Picturepark.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Picturepark**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Picturepark**.

    ![Link Picturepark na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-picturepark-test-user"></a>Tworzenie użytkownika testowego Picturepark

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Picturepark, musi być obsługiwana w Picturepark. W przypadku Picturepark Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **Picturepark** dzierżawy.

1. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **użytkowników**.
   
    ![Użytkownicy](./media/picturepark-tutorial/ic795067.png "Użytkownicy")

1. W **Przegląd użytkowników** kliknij pozycję **New**.
   
    ![Zarządzanie użytkownikami](./media/picturepark-tutorial/ic795068.png "Zarządzanie użytkownikami")

1. Na **Create User** okna dialogowego, wykonaj następujące kroki prawidłowego usługi Azure Active Directory użytkownika ma aprowizację:
   
    ![Utwórz użytkownika](./media/picturepark-tutorial/ic795069.png "Utwórz użytkownika")
   
    a. W **adres E-mail** polu tekstowym wpisz **adres e-mail** użytkownika `BrittaSimon@contoso.com`.  
   
    b. W **hasło** i **Potwierdź hasło** pola tekstowe, wpisz **hasło** z BrittaSimon. 
   
    c. W **imię** polu tekstowym wpisz **imię** użytkownika **Britta**. 
   
    d. W **nazwisko** polu tekstowym wpisz **nazwisko** użytkownika **Simon**.
   
    e. W **firmy** polu tekstowym wpisz **nazwa firmy** użytkownika. 
   
    f. W **kraju** pola tekstowego, wybierz opcję **Kraj/Region** użytkownika.
  
    g. W **ZIP** polu tekstowym wpisz **kod POCZTOWY** miasta.
   
    h. W **Miasto** polu tekstowym wpisz **nazwę miejscowości** użytkownika.

    i. Wybierz **języka**.
   
    j. Kliknij pozycję **Utwórz**.

>[!NOTE]
>Można użyć jakichkolwiek innych Picturepark użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Picturepark można uaktywniać ich konta usługi Azure AD.
>

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Picturepark w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Picturepark, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

