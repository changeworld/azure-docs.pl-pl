---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Bonusly | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 298f25e151d4c52749d646a9457f0962c68bf4d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106144"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Bonusly

W tym samouczku dowiesz się, jak zintegrować Bonusly w usłudze Azure Active Directory (Azure AD).
Integrowanie Bonusly z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Bonusly.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Bonusly (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Bonusly, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Bonusly logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje bonusly **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-bonusly-from-the-gallery"></a>Dodawanie Bonusly z galerii

Aby skonfigurować integrację Bonusly w usłudze Azure AD, należy dodać Bonusly z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Bonusly z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Bonusly**, wybierz opcję **Bonusly** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Bonusly na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Bonusly w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Bonusly musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Bonusly, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Bonusly logowania jednokrotnego](#configure-bonusly-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Bonusly](#create-bonusly-test-user)**  — aby odpowiednikiem Britta Simon w Bonusly połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Bonusly, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Bonusly** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Bonusly domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-reply.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Bonusly](https://bonus.ly/contact) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. Na **Konfigurowanie Bonusly** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-bonusly-single-sign-on"></a>Konfigurowanie Bonusly logowania jednokrotnego

1. W oknie innej przeglądarki, zaloguj się do Twojej **Bonusly** dzierżawy.

1. Na pasku narzędzi u góry kliknij **ustawienia** , a następnie wybierz **integracje i aplikacje**.

    ![Bonusly sekcji społecznościowych](./media/bonus-tutorial/ic773686.png "Bonusly")
1. W obszarze **logowania jednokrotnego**, wybierz opcję **SAML**.

1. Na **SAML** okna dialogowego strony, wykonaj następujące czynności:

    ![Bonusly strony okna dialogowego Saml](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. W **logowania jednokrotnego dostawcy tożsamości, docelowy adres URL** pole tekstowe, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    b. W **adres URL logowania dostawcy tożsamości** pole tekstowe, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    c. W **wystawca dostawcy tożsamości** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal.
    
    d. Wklej **odcisk palca** wartość kopiowana z witryny Azure portal do **odcisk palca certyfikatu** pola tekstowego.
    
    e. Kliknij pozycję **Zapisz**.

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Bonusly.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Bonusly**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Bonusly**.

    ![Bonusly łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-bonusly-test-user"></a>Tworzenie użytkownika testowego Bonusly

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Bonusly, musi być obsługiwana w Bonusly. W przypadku Bonusly Inicjowanie obsługi administracyjnej jest zadanie ręczne.

> [!NOTE]
> Można użyć jakichkolwiek innych Bonusly użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Bonusly do aprowizacji kont użytkowników usługi AAD. 

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. W oknie przeglądarki sieci web Zaloguj się do Bonusly dzierżawy.

1. Kliknij pozycję **Ustawienia**.

    ![Ustawienia](./media/bonus-tutorial/ic781041.png "Ustawienia")

1. Kliknij przycisk **użytkowników i premie** kartę.

    ![Użytkownicy i premie](./media/bonus-tutorial/ic781042.png "użytkowników i dodatki")

1. Kliknij przycisk **Zarządzanie użytkownikami**.

    ![Zarządzanie użytkownikami](./media/bonus-tutorial/ic781043.png "Zarządzanie użytkownikami")

1. Kliknij przycisk **Dodaj użytkownika**.

    ![Dodawanie użytkownika](./media/bonus-tutorial/ic781044.png "Dodawanie użytkownika")

1. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Dodawanie użytkownika](./media/bonus-tutorial/ic781045.png "Dodawanie użytkownika")  

    a. W polu tekstowym **First name (Imię)** wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.

    c. W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak `brittasimon\@contoso.com`.

    d. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Właściciel konta usługi Azure AD, otrzymuje wiadomość e-mail zawierającą link do potwierdzenia konta, zanim stanie się aktywny.  

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Bonusly w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Bonusly, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
