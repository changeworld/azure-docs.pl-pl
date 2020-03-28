---
title: 'Samouczek: Integracja usługi Azure Active Directory z tableau online | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Tableau Online.
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
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985615"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą Tableau Online

W tym samouczku dowiesz się, jak zintegrować tableau online z usługą Azure Active Directory (Azure AD). Po zintegrowaniu tableau online z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Usługi Tableau Online.
* Włącz użytkownikom automatyczne logowanie do usługi Tableau Online za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego Tableau Online (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Tableau Online obsługuje sygosk SSO inicjowane przez **usługę SP**
* Po skonfigurowaniu usługi Tableau Online można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Dodawanie Tableau Online z galerii

Aby skonfigurować integrację tableau online z usługą Azure AD, należy dodać Tableau Online z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **tableau online** w polu wyszukiwania.
1. Wybierz **pozycję Tableau Online** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Tableau Online na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Tableau Online.

Aby skonfigurować i przetestować sytua usługi Azure AD w usłudze Tableau Online, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne tableau online](#configure-tableau-online-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Tableau Online](#create-tableau-online-test-user)** — aby mieć odpowiednik B.Simon w Tableau Online, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi Tableau Online, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Tableau Online** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Tableau Online Domain i adresy URL — informacje o loguchu jednokrotnym](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > `<entityid>` Otrzymasz wartość z **sekcji Konfigurowanie Tableau Online** w tym samouczku. Wartością identyfikatora jednostki będzie wartość **identyfikatora usługi Azure AD** w sekcji **Konfigurowanie usługi Tableau Online.**

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie usługi Tableau Online** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **\@brittasimon yourcompanydomain.extension**  
    Na przykład BrittaSimon\@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do Tableau Online.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję Tableau Online**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Tableau Online**.

    ![Łącze Tableau Online na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-tableau-online-sso"></a>Konfigurowanie usługi Tableau Online SSO

1. W innym oknie przeglądarki zaloguj się do aplikacji Tableau Online. Przejdź do **ustawień,** a następnie **uwierzytelnianie**.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Aby włączyć SAML, w obszarze **uwierzytelniania sekcji typy.** Zaznacz **pozycję Włącz dodatkową metodę uwierzytelniania,** a następnie zaznacz pole wyboru **SAML.**

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Przewiń w dół w górę do **sekcji Importuj pliki metadanych do sekcji Tableau Online.**  Kliknij przycisk Przeglądaj i zaimportuj plik metadanych, który został pobrany z usługi Azure AD. Następnie kliknij przycisk **Zastosuj**.

   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. W sekcji **Dopasuj potwierdzenia** wstaw odpowiednią nazwę potwierdzenia dostawcy tożsamości dla **adresu e-mail,** **imienia**i **nazwiska**. Aby uzyskać te informacje z usługi Azure AD: 
  
    a. W witrynie Azure portal przejdź na stronę integracji aplikacji **Tableau Online.**

    b. W sekcji **Atrybuty użytkownika & Oświadczenia** kliknij ikonę edycji.

   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection.png)

    d. Skopiuj wartość obszaru nazw dla tych atrybutów: podanana, adres e-mail i nazwisko, wykonując następujące czynności:

   ![Logowanie jednokrotne usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kliknij wartość **user.givenname**

    e. Skopiuj wartość z pola **tekstowego Obszar nazw.**

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection2.png)

    f. Aby skopiować wartości obszaru nazw dla wiadomości e-mail i nazwiska, powtórz powyższe kroki.

    g. Przełącz się do aplikacji Tableau Online, a następnie ustaw sekcję **Atrybuty & Oświadczenia użytkownika** w następujący sposób:

    * **E-mail: poczta** lub **nazwa użytkownika**

    * Imię: **imię**

    * Nazwisko: **nazwisko**

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Utwórz użytkownika testowego Tableau Online

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Tableau Online.

1. W **aplikacji Tableau Online**kliknij pozycję **Ustawienia,** a następnie sekcja **Uwierzytelnianie.** Przewiń w dół do sekcji **Zarządzaj użytkownikami.** Kliknij **pozycję Dodaj użytkowników,** a następnie kliknij pozycję **Wprowadź adresy e-mail**.
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Wybierz **pozycję Dodaj użytkowników do uwierzytelniania (SAML).** W **obszarze Wprowadź adresy e-mail** dodaj contoso.com britta.simon\@
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kliknij **pozycję Dodaj użytkowników**.

### <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Tableau Online w Panelu dostępu należy automatycznie zalogować się do usługi Tableau Online, dla której skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)