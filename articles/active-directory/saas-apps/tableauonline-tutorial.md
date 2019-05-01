---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Tableau Online | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 352ad9473a1c1a9360ddceb720ff968f4e97e012
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64876641"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Tableau Online

W tym samouczku dowiesz się, jak zintegrować Tableau Online z usługą Azure Active Directory (Azure AD).
Integrowanie Tableau Online z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Online firmy Tableau.
* Użytkownikom można automatycznie zalogowany do usługi Online firmy Tableau (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Tableau Online, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* TABLEAU Online logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Online TABLEAU **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-tableau-online-from-the-gallery"></a>Dodawanie Tableau Online za pomocą galerii

Aby skonfigurować integrację Tableau online w usłudze Azure AD, należy dodać Tableau Online z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Tableau Online z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Tableau Online**, wybierz opcję **Tableau Online** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![TABLEAU Online na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowania i testowania usługi Azure AD logowanie jednokrotne z usługą Online Tableau w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w dokumentacji Online Tableau musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą Tableau Online, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Tableau Online logowania jednokrotnego](#configure-tableau-online-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Tableau Online](#create-tableau-online-test-user)**  — aby odpowiednikiem Britta Simon w Tableau Online, które jest połączone z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne z usługą Tableau Online, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Tableau Online** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Adresy URL i domen w trybie Online TABLEAU pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Zostanie wyświetlony `<entityid>` wartość z **Konfigurowanie Tableau Online** sekcji, w tym samouczku. Wartość Identyfikatora jednostki będą **usługi Azure AD identyfikator** wartość w **Konfigurowanie Tableau Online** sekcji.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Na **Konfigurowanie Tableau Online** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-tableau-online-single-sign-on"></a>Konfigurowanie Tableau Online logowania jednokrotnego

1. W oknie innej przeglądarki logowanie jednokrotne do aplikacji Tableau Online. Przejdź do **ustawienia** i następnie **uwierzytelniania**.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Aby włączyć protokół SAML, w obszarze **typy uwierzytelniania** sekcji. Sprawdź **włączyć dodatkową metodę uwierzytelniania** , a następnie sprawdź **SAML** pola wyboru.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Przewiń w dół do **Importuj plik metadanych do usługi Online firmy Tableau** sekcji.  Kliknij przycisk Przeglądaj, a następnie Importuj plik metadanych, który został pobrany z usługi Azure AD. Następnie kliknij przycisk **Zastosuj**.

   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. W **dopasowania potwierdzenia** sekcji, Wstaw nazwę odpowiedniego potwierdzenie dostawcy tożsamości dla **adres e-mail**, **imię**, i **nazwisko**. Aby uzyskać te informacje z usługi Azure AD: 
  
    a. W witrynie Azure portal, przejdź na **Tableau Online** strony integracji aplikacji.

    b. W **atrybutów użytkowników i oświadczeń** kliknij ikonę edycji.

   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection.png)

    c. Skopiuj wartość przestrzeni nazw dla tych atrybutów: givenname, poczty e-mail i nazwisko wykonując następujące kroki:

   ![Usługa Azure AD logowanie jednokrotne](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kliknij przycisk **user.givenname** wartość

    e. Skopiuj wartości z **Namespace** pola tekstowego.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection2.png)

    f. Aby skopiować przestrzeń nazw wartości dla poczty e-mail i nazwisko, powtórz powyższe kroki.

    g. Przełącz się do aplikacji firmy Tableau Online, a następnie ustaw **atrybutów użytkowników i oświadczeń** sekcji w następujący sposób:

    * Adres e-mail: **poczty** lub **userprincipalname**

    * Imię: **givenname**

    * Nazwisko: **nazwisko**

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

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
    Na przykład BrittaSimon\@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Online firmy Tableau.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Tableau Online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Tableau Online**.

    ![Tableau Online łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-tableau-online-test-user"></a>Tworzenie użytkownika testowego Tableau Online

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Tableau w trybie Online.

1. Na **Tableau Online**, kliknij przycisk **ustawienia** i następnie **uwierzytelniania** sekcji. Przewiń w dół do **Zarządzanie użytkownikami** sekcji. Kliknij przycisk **Add Users** a następnie kliknij przycisk **wprowadź adresy E-mail**.
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Wybierz **Dodawanie użytkowników do uwierzytelniania (SAML)**. W **wprowadź adresy e-mail** textbox Dodaj britta.simon\@contoso.com
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kliknij przycisk **dodawania użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Tableau Online, w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze programu Tableau w trybie Online dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
