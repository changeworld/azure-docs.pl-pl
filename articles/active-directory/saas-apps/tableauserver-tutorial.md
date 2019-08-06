---
title: 'Samouczek: Azure Active Directory integrację z serwerem Tableau | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i serwerem Tableau.
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
ms.openlocfilehash: f9ef179c1a93d8b2f97c47eb4c68d0312d55d3d1
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825984"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Samouczek: Integracja Azure Active Directory z serwerem Tableau

W tym samouczku dowiesz się, jak zintegrować serwer Tableau z usługą Azure Active Directory (Azure AD).
Integracja serwera Tableau z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do serwera Tableau.
* Możesz umożliwić użytkownikom automatyczne logowanie do serwera Tableau (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z serwerem Tableau, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym na serwerze Tableau

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Serwer Tableau obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Dodawanie serwera Tableau z galerii

Aby skonfigurować integrację serwera Tableau z usługą Azure AD, musisz dodać serwer Tableau z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać serwer Tableau z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Tableau Server**, wybierz opcję **serwer Tableau** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Serwer Tableau na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD przy użyciu serwera Tableau na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na serwerze Tableau.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu serwera Tableau, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-tableau-server-single-sign-on)** jednokrotne na serwerze Tableau — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego serwera Tableau](#create-tableau-server-test-user)** , aby uzyskać odpowiednik usługi Britta Simon na serwerze Tableau, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu serwera Tableau, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **serwera Tableau** wybierz pozycję **Logowanie**jednokrotne.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje dotyczące logowania jednokrotnego i adresów URL serwera Tableau](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Poprzednie wartości nie są wartościami rzeczywistymi. Zaktualizuj wartości z rzeczywistym adresem URL i identyfikatorem na stronie konfiguracji serwera Tableau, która została omówiona w dalszej części tego samouczka.

5. Aplikacja serwera Tableau oczekuje niestandardowej **nazwy użytkownika** , która musi być zdefiniowana poniżej. Jest on używany jako identyfikator użytkownika zamiast unikatowego żądania identyfikatora użytkownika. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika & oświadczenia** .

    ![image](common/edit-attribute.png)

6. W sekcji **oświadczenia użytkownika** w oknie dialogowym **atrybuty użytkownika & oświadczenia** Skonfiguruj atrybut tokenu SAML, jak pokazano na powyższym obrazie, i wykonaj następujące czynności:

    | Name (Nazwa) | Atrybut źródłowy | Przestrzeń nazw |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Wprowadź wartość **przestrzeni nazw** .

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij polecenie **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

8. W sekcji **Konfigurowanie serwera Tableau** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurowanie logowania jednokrotnego na serwerze Tableau

1. Aby skonfigurować Logowanie jednokrotne dla aplikacji, musisz zalogować się do dzierżawy serwera Tableau jako administrator.

2. Na karcie **Konfiguracja** wybierz pozycję **tożsamość użytkownika & dostęp**, a następnie wybierz kartę Metoda **uwierzytelniania** .

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na stronie **Konfiguracja** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. W obszarze **Metoda uwierzytelniania**wybierz pozycję SAML.

    b. Zaznacz pole wyboru **Włącz uwierzytelnianie SAML dla serwera**.

    c. Zwrotny adres URL serwera Tableau — adres URL, do którego będą uzyskiwać dostęp użytkownicy serwera <http://tableau_server>Tableau, na przykład. Użycie `http://localhost` nie jest zalecane. Użycie adresu URL z końcowym ukośnikiem (na przykład `http://tableau_server/`) nie jest obsługiwane. Skopiuj **adres URL zwrotnego serwera Tableau** i wklej go w celu zalogowania się do **adresu URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje instalację serwera Tableau w dostawcy tożsamości. Możesz tutaj wprowadzić adres URL serwera Tableau, jeśli chcesz, ale nie musi to być adres URL serwera Tableau. Skopiuj **Identyfikator jednostki SAML** i wklej go do pola tekstowego **identyfikatora** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    e. Kliknij **plik metadanych XML pobierania** i otwórz go w aplikacji Edytor tekstu. Znajdź adres URL usługi Konsumenckej potwierdzenia przy użyciu protokołu HTTP POST i indeksu 0 i skopiuj adres URL. Teraz wklej je do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    f. Znajdź plik metadanych Federacji pobrany z Azure Portal, a następnie Przekaż go w **pliku metadanych SAML dostawcy tożsamości**.

    g. Wprowadź nazwy atrybutów, których dostawcy tożsamości używa do przechowywania nazw użytkowników, nazw wyświetlanych i adresów e-mail.

    h. Kliknij polecenie **Zapisz**.

    > [!NOTE]
    > Klient musi przekazać dowolny certyfikat z konfiguracji SSO protokołu SAML serwera Tableau i zostanie zignorowany w przepływie logowania jednokrotnego. Jeśli potrzebujesz pomocy przy konfigurowaniu protokołu SAML na serwerze Tableau, zapoznaj się z tym artykułem [Konfigurowanie protokołu SAML](https://help.tableau.com/current/server/en-gb/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz`brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do serwera Tableau.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **serwer Tableau**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **serwer Tableau**.

    ![Link serwera Tableau na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-tableau-server-test-user"></a>Utwórz użytkownika testowego serwera Tableau

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon na serwerze Tableau. Musisz udostępnić wszystkim użytkownikom na serwerze Tableau.

Ta nazwa użytkownika powinna być zgodna z wartością skonfigurowaną w niestandardowym atrybucie usługi Azure AD **nazwy użytkownika**. Przy prawidłowym mapowaniu należy wykonać integrację, aby skonfigurować Logowanie jednokrotne w usłudze Azure AD.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z administratorem serwera Tableau w organizacji.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka serwer Tableau w panelu dostępu należy automatycznie zalogować się do serwera Tableau, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

