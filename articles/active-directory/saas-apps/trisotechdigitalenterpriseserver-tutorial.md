---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Trisotech cyfrowego Enterprise Server | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Trisotech cyfrowe serwera Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: c5d1a791de5c295b4644e5633983a5369200498e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256587"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Trisotech cyfrowego Enterprise Server

W tym samouczku dowiesz się, jak zintegrować Trisotech cyfrowe serwera przedsiębiorstwa za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie Trisotech cyfrowego Enterprise Server z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Trisotech cyfrowe serwera przedsiębiorstwa.
* Użytkownikom można automatycznie zalogowany Trisotech cyfrowego Enterprise Server (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Trisotech cyfrowego Enterprise Server, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Trisotech cyfrowe serwera przedsiębiorstwa logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Trisotech cyfrowego Enterprise Server **SP** jednokrotne logowanie inicjowane przez

* Obsługuje Trisotech cyfrowego Enterprise Server **Just In Time** aprowizacji użytkowników

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Dodawanie Trisotech cyfrowego Enterprise Server z galerii

Aby skonfigurować integrację Trisotech cyfrowego Enterprise Server w usłudze Azure AD, należy dodać Trisotech cyfrowego Enterprise Server z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Trisotech cyfrowego Enterprise Server z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Trisotech cyfrowego Enterprise Server**, wybierz opcję **Trisotech cyfrowego Enterprise Server** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

     ![Trisotech cyfrowe Enterprise serwera na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Trisotech cyfrowego Server Enterprise w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Trisotech cyfrowego Enterprise Server musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Trisotech cyfrowego Enterprise Server, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Trisotech cyfrowego serwera logowania jednokrotnego dla firm](#configure-trisotech-digital-enterprise-server-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Trisotech cyfrowego Enterprise Server](#create-trisotech-digital-enterprise-server-test-user)**  — aby odpowiednikiem Britta Simon w Trisotech cyfrowego Enterprise Server, która jest połączona z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Trisotech cyfrowego Enterprise Server, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Trisotech cyfrowego Enterprise Server** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Trisotech cyfrowego Enterprise Server domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.trisotech.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Trisotech cyfrowego Enterprise Server Client](mailto:support@trisotech.com) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Konfigurowanie cyfrowy Trisotech Enterprise serwera logowania jednokrotnego

1. W oknie przeglądarki internetowej innej Zaloguj się w witryny firmy Trisotech cyfrowego Enterprise serwera konfiguracji jako administrator.

2. Kliknij pozycję **ikonę Menu** , a następnie wybierz **administracji**.

    ![Konfigurowanie logowania jednokrotnego](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Wybierz **dostawca użytkownika**.

    ![Konfigurowanie logowania jednokrotnego](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. W **konfiguracji dostawcy użytkowników** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Wybierz **zabezpieczone Assertion Markup języka 2 (SAML 2)** z listy rozwijanej w **metodę uwierzytelniania**.

    b. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartość, która została skopiowana tworzą witryny Azure portal.

    c. W **identyfikator aplikacji** polu tekstowym wprowadź adres URL, za pomocą następującego wzorca: `https://<companyname>.trisotech.com`.

    d. Kliknij pozycję **Zapisz**

    e. Wprowadź nazwę domeny w **domen z dozwolone (wartość empty oznacza każdy)** pola tekstowego, automatycznie przypisuje licencji dla użytkowników zgodnych domen dozwolone

    f. Kliknij pozycję **Zapisz**

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Trisotech cyfrowego Enterprise Server.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Trisotech cyfrowego Enterprise Server**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Trisotech cyfrowego Enterprise Server**.

    ![Link Trisotech cyfrowego Enterprise Server, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Tworzenie użytkownika testowego Trisotech cyfrowego Enterprise Server

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w Trisotech cyfrowe serwera przedsiębiorstwa. Trisotech cyfrowego Enterprise Server obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Trisotech cyfrowego Enterprise Server, nowy katalog jest tworzony po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Trisotech cyfrowego Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Trisotech cyfrowego Enterprise Server, w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Trisotech cyfrowe serwera przedsiębiorstwa dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

