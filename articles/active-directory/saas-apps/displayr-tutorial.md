---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Displayr | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9694790ea02bc778bf3b9db212e61fabb90a28a8
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441430"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Displayr

W tym samouczku dowiesz się, jak zintegrować Displayr w usłudze Azure Active Directory (Azure AD).
Integrowanie Displayr z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Displayr.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Displayr (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Displayr, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Displayr logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Displayr **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-displayr-from-the-gallery"></a>Dodawanie Displayr z galerii

Aby skonfigurować integrację Displayr w usłudze Azure AD, należy dodać Displayr z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Displayr z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Displayr**, wybierz opcję **Displayr** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Displayr na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Displayr w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Displayr musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Displayr, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Displayr logowania jednokrotnego](#configure-displayr-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Displayr](#create-displayr-test-user)**  — aby odpowiednikiem Britta Simon w Displayr połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Displayr, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Displayr** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujący krok:

    ![Displayr domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-intiated.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://app.displayr.com/Login`

5. W **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać **certyfikatu (Raw)** z danymi opcjami zgodnie z wymaganiami, a następnie zapisz go w sieci komputer.

    ![Link pobierania certyfikatu](common/certificateraw.png)

6. Aplikacja Displayr oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

7. Ponadto powyżej Displayr aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W **atrybutów użytkowników i oświadczeń** sekcji na **oświadczenia grupy (wersja zapoznawcza)** okno dialogowe, należy wykonać następujące czynności:

    a. Kliknij przycisk **pióra** obok **grup zwracane w oświadczenie**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Wybierz **wszystkich grup** z listy opcji.

    c. Wybierz **atrybutu źródła** z **identyfikator grupy**.

    d. Sprawdź **dostosować nazwy oświadczenia grupy**.

    e. Sprawdź **emisji grupy jako oświadczenia roli**.

    f. Kliknij pozycję **Zapisz**.

8. Na **Displayr konfiguracji** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-displayr-single-sign-on"></a>Konfigurowanie Displayr logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się w Displayr jako Administrator.

2. Kliknij pozycję **ustawienia** a następnie przejdź do **konta**.

    ![Konfigurowanie](./media/displayr-tutorial/config01.png)

3. Przełącz się do **ustawienia** z górnego menu i przewiń w dół strony kliknięcie **konfigurowanie pojedynczego logowania na (SAML)**.

    ![Konfigurowanie](./media/displayr-tutorial/config02.png)

4. Na **pojedynczy znak na (SAML)** strony, wykonaj następujące czynności:

    ![Konfigurowanie](./media/displayr-tutorial/config03.png)

    a. Sprawdź **Włącz pojedynczy znak na (SAML)** pole.

    b. W **wystawcy** tekstu pole, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal.

    c. W **adres URL logowania** tekstu pole, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    d. W **adres URL wylogowania** tekstu pole, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. Otwórz certyfikat (Raw) w programie Notatnik, skopiuj jego zawartość i wklej go do **certyfikatu** pola tekstowego.

    f. **Grupy mapowania** są opcjonalne.

    g. Kliknij pozycję **Zapisz**.  

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Displayr.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Displayr**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Displayr**.

    ![Link Displayr na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-displayr-test-user"></a>Tworzenie użytkownika testowego Displayr

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się w celu Displayr, ich musi być obsługiwana w Displayr. W Displayr Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Displayr jako Administrator.

2. Kliknij pozycję **ustawienia** a następnie przejdź do **konta**.

    ![Konfiguracja Displayr](./media/displayr-tutorial/config01.png)

3. Przełącz się do **ustawienia** w górnym menu i przewiń stronę w dół do momentu **użytkowników** sekcji, a następnie kliknij pozycję **nowego użytkownika**.

    ![Konfiguracja Displayr](./media/displayr-tutorial/config07.png)

4. Na **nowego użytkownika** strony, wykonaj następujące czynności:

    ![Konfiguracja Displayr](./media/displayr-tutorial/config06.png)

    a. W **nazwa** tekstu wprowadź nazwę użytkownika, takich jak **Brittasimon**.

    b. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak `Brittasimon@contoso.com`.

    c. Wybierz swoje odpowiednie **członkostwo w grupie**.

    d. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Displayr w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Displayr, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

