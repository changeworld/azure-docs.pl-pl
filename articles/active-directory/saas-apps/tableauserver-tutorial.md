---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Tableau Server | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 539a06398675dc7851017ec5d428e0942e54ce1f
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564769"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Tableau Server

W tym samouczku dowiesz się, jak zintegrować Tableau Server w usłudze Azure Active Directory (Azure AD).
Integrowanie Tableau Server z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do serwera Tableau.
* Użytkownikom można automatycznie zalogowany do Tableau Server (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Tableau Server, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* TABLEAU Server logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Serwer firmy TABLEAU obsługuje **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-tableau-server-from-the-gallery"></a>Dodawanie Tableau Server z galerii

Aby skonfigurować integrację Tableau Server w usłudze Azure AD, należy dodać Tableau Server z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać serwer firmy Tableau z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Tableau Server**, wybierz opcję **Tableau Server** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Serwer firmy TABLEAU na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Tableau Server, w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika Tableau Server musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Tableau Server, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Tableau Server logowania jednokrotnego](#configure-tableau-server-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Tableau Server](#create-tableau-server-test-user)**  — aby odpowiednikiem Britta Simon w serwer firmy Tableau, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Tableau Server, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Tableau Server** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![TABLEAU Server domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Powyższe wartości nie są rzeczywiste wartości. Zaktualizuj wartości przy użyciu rzeczywistego adresu URL i identyfikator na stronie konfiguracji Tableau Server, które zostało wyjaśnione w dalszej części tego samouczka.

5. TABLEAU Server aplikacja oczekuje oświadczenia niestandardowego **username** który musi być zdefiniowana poniżej. To jest on używany jako identyfikator użytkownika zamiast oświadczenie identyfikatora unikatowego użytkownika. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

    ![image](common/edit-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    | Name (Nazwa) | Atrybut źródłowy | Przestrzeń nazw |
    | ---------------| --------------- | ----------- |
    | nazwa użytkownika | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Wprowadź **Namespace** wartość.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

8. Na **skonfigurować serwer firmy Tableau** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurowanie Tableau Server logowania jednokrotnego

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, musisz zalogować się do dzierżawy Tableau Server jako administrator.

2. Na **konfiguracji** zaznacz **użytkownika tożsamość i dostęp**, a następnie wybierz pozycję **uwierzytelniania** kartę metoda.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na **konfiguracji** strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Aby uzyskać **metodę uwierzytelniania**, wybierz SAML.

    b. Zaznacz pole wyboru z **włączyć uwierzytelnianie SAML serwera**.

    c. Serwer firmy TABLEAU zwrotny adres URL — adres URL, który serwer firmy Tableau użytkownicy będą uzyskiwać dostęp do, takich jak <http://tableau_server>. Za pomocą `http://localhost` nie jest zalecane. Przy użyciu adresu URL kończących się ukośnikiem (na przykład `http://tableau_server/`) nie jest obsługiwane. Kopiuj **Tableau Server zwrotny adres URL** i wklej go do **na adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal

    d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje serwer firmy Tableau instalacji dostawcy tożsamości. Możesz wprowadzić adres URL serwera Tableau ponownie, jeśli chcesz, ale nie musi być adresem URL Twojej Tableau Server. Kopiuj **SAML identyfikator jednostki** i wklej go do **identyfikator** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal

    e. Kliknij przycisk **pobrać pliku metadanych XML** i otwórz go w aplikacji edytora tekstu. Znajdź adres URL usługi konsumenta potwierdzenia z żądania Http Post i indeks 0 i skopiuj adres URL. Teraz wklej ją w celu **adres URL odpowiedzi** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal

    f. Zlokalizuj plik metadanych Federacji, pobrany z witryny Azure portal, a następnie przekaż go w **plik metadanych SAML dostawcy tożsamości**.

    g. Wprowadź nazwę dla atrybutów, których dostawca tożsamości używa do przechowywania nazw użytkowników, nazw wyświetlanych i adresów e-mail.

    h. Kliknij pozycję **Zapisz**

    > [!NOTE]
    > Klient musi przekazać dowolny certyfikat w konfiguracji logowania jednokrotnego SAML serwer firmy Tableau i będą Pobierz ignorowane w przepływie logowania jednokrotnego. Jeśli potrzebujesz pomoc dotyczącą konfigurowania SAML na serwer firmy Tableau można znaleźć w tym artykule [skonfigurować SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do serwera Tableau.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Tableau Server**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Tableau Server**.

    ![Serwer firmy Tableau łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-tableau-server-test-user"></a>Tworzenie użytkownika testowego Tableau Server

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon Tableau Server. Należy aprowizować wszystkich użytkowników na serwerze Tableau.

Że nazwa użytkownika powinna odpowiadać wartości, które zostały skonfigurowane w atrybucie niestandardowym usługi Azure AD z **username**. Za pomocą mapowania poprawne integracji powinny działać usługi Azure AD Konfigurowanie logowania jednokrotnego.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, należy skontaktować się z administratorem Tableau Server w Twojej organizacji.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Tableau Server w panelu dostępu, możesz powinny być automatycznie zalogowany do serwera Tableau, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

