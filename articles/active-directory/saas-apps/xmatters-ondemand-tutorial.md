---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą xMatters OnDemand | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086515"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą xMatters na żądanie

W tym samouczku dowiesz się, jak zintegrować xMatters na żądanie przy użyciu usługi Azure Active Directory (Azure AD).
Integrowanie xMatters na żądanie z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do xMatters OnDemand.
* Aby umożliwić użytkownikom można automatycznie zalogowany do xMatters OnDemand (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą xMatters OnDemand, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* xMatters OnDemand pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* xMatters obsługuje OnDemand **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Dodawanie xMatters OnDemand z galerii

Aby skonfigurować integrację xMatters na żądanie w usłudze Azure AD, należy dodać xMatters OnDemand z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać xMatters na żądanie w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **xMatters OnDemand**, wybierz opcję **xMatters OnDemand** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![xMatters OnDemand na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą xMatters OnDemand w oparciu o nazwie użytkownika testowego **Britta Simon**.
Do logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w xMatters OnDemand musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą xMatters na żądanie, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie xMatters na żądanie logowania jednokrotnego](#configure-xmatters-ondemand-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego OnDemand xMatters](#create-xmatters-ondemand-test-user)**  — aby mają odpowiednika w pozycji Britta simon w xMatters OnDemand połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z xMatters na żądanie, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **xMatters OnDemand** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![xMatters OnDemand domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołu pomocy technicznej xMatters klienta OnDemand](https://www.xmatters.com/company/contact-us/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

    > [!IMPORTANT]
    > Należy przekazywać certyfikat [zespołu pomocy technicznej xMatters OnDemand](https://www.xmatters.com/company/contact-us/). Ten certyfikat musi przekazywanych przez zespół pomocy technicznej xMatters przed można zakończyć jednej konfiguracji logowania jednokrotnego.

6. Na **Konfigurowanie xMatters OnDemand** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurowanie xMatters na żądanie logowania jednokrotnego

1. W oknie przeglądarki internetowej innej zarejestrować się do witryny firmy XMatters OnDemand jako administrator.

2. Na pasku narzędzi u góry kliknij **administratora**, a następnie kliknij przycisk **szczegóły firmy** na pasku nawigacyjnym po lewej stronie.

    ![Administrator](./media/xmatters-ondemand-tutorial/IC776795.png "Administrator")

3. Na stronie **SAML Configuration** (Konfiguracja SAML) wykonaj następujące kroki:

    ![Plik konfiguracji SAML](./media/xmatters-ondemand-tutorial/IC776796.png "plik konfiguracji SAML")

    a. Wybierz pozycję **Enable SAML** (Włącz SAML).

    b. W **identyfikator dostawcy tożsamości** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    c. W **pojedynczy znak na adres URL** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    d. W **adres URL wylogowania jednokrotnego** pola tekstowego, Wklej **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. Na stronie Szczegóły firmy u góry strony, kliknij przycisk **Zapisz zmiany**.

    ![Szczegóły firmy](./media/xmatters-ondemand-tutorial/IC776797.png "firmy szczegóły")

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do xMatters OnDemand.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **xMatters OnDemand**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **xMatters OnDemand**.

    ![XMatters OnDemand łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-xmatters-ondemand-test-user"></a>Tworzenie użytkownika testowego OnDemand xMatters

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w xMatters OnDemand.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **XMatters OnDemand** dzierżawy.

2. Kliknij przycisk **użytkowników** kartę, a następnie kliknij przycisk **Dodaj użytkownika**.

    ![Użytkownicy](./media/xmatters-ondemand-tutorial/IC781048.png "Użytkownicy")

3. W **Dodawanie użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodawanie użytkownika](./media/xmatters-ondemand-tutorial/IC781049.png "Dodawanie użytkownika")

    a. Wybierz pozycję **Active** (Aktywne).

    b. W **identyfikator użytkownika** polu tekstowym wpisz identyfikator użytkownika użytkownika, takie jak Brittasimon@contoso.com.

    c. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, takie jak Britta.

    d. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    e. W **witryny** polu tekstowym Wprowadź prawidłową witryną prawidłowe platformy Azure konto usługi AD do aprowizowania.

    f. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu xMatters kafelka na żądanie w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze OnDemand xMatters, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

