---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Tableau Server | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: c727cddf41c269c214b541134cd9f688017ee687
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789725"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Tableau Server

W tym samouczku dowiesz się, jak zintegrować Tableau Server w usłudze Azure Active Directory (Azure AD).

Integrowanie Tableau Server z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do serwera Tableau.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Tableau Server (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Tableau Server, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Serwer firmy Tableau logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Tableau Server z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-tableau-server-from-the-gallery"></a>Dodawanie Tableau Server z galerii

Aby skonfigurować integrację Tableau Server w usłudze Azure AD, należy dodać Tableau Server z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać serwer firmy Tableau z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Tableau Server**, wybierz opcję **Tableau Server** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Serwer firmy TABLEAU na liście wyników](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Tableau Server, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Tableau Server dla użytkownika w usłudze Azure AD. Oznacza to relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika Tableau Server musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Tableau Server, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Tableau Server logowania jednokrotnego](#configure-tableau-server-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Tworzenie użytkownika testowego Tableau Server](#create-tableau-server-test-user)**  — aby odpowiednikiem Britta Simon w parasola Cisco, połączonego z usługi Azure AD reprezentacja użytkownika.
5. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji serwer firmy Tableau.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Tableau Server, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Tableau Server** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial-general-301.png)

3. TABLEAU Server aplikacja oczekuje oświadczenia niestandardowego **username** który musi być zdefiniowana poniżej. To jest on używany jako identyfikator użytkownika zamiast oświadczenie identyfikatora unikatowego użytkownika. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Kliknij przycisk **Edytuj** przycisk, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw |
    | ---------------| --------------- | ----------- |   
    | nazwa użytkownika | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Wprowadź **Namespace** wartość.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij pozycję **Zapisz**.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

6. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link`
    
    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://azure.<domain name>.link`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Powyższe wartości nie są rzeczywiste wartości. Zaktualizuj wartości przy użyciu rzeczywistego adresu URL i identyfikator na stronie konfiguracji Tableau Server, które zostało wyjaśnione w dalszej części tego samouczka.

7. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png)

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurowanie Tableau Server logowania jednokrotnego 

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, musisz logowanie jednokrotne do swojej dzierżawy Tableau Server jako administrator.

2. Na **konfiguracji** zaznacz **użytkownika tożsamość i dostęp**, a następnie wybierz pozycję **uwierzytelniania** kartę metoda.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na **konfiguracji** strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Aby uzyskać **metodę uwierzytelniania**, wybierz SAML.
    
    b. Zaznacz pole wyboru z **włączyć uwierzytelnianie SAML serwera**.

    c. Serwer firmy TABLEAU zwrotny adres URL — adres URL, który serwer firmy Tableau użytkownicy będą uzyskiwać dostęp do, takich jak http://tableau_server. Za pomocą http://localhost nie jest zalecane. Przy użyciu adresu URL kończących się ukośnikiem (na przykład http://tableau_server/) nie jest obsługiwane. Kopiuj **Tableau Server zwrotny adres URL** i wkleić go do usługi Azure AD **na adres URL logowania** polu tekstowym w **Tableau Server domena i adresy URL** sekcji.

    d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje serwer firmy Tableau instalacji dostawcy tożsamości. Możesz wprowadzić adres URL serwera Tableau ponownie, jeśli chcesz, ale nie musi być adresem URL Twojej Tableau Server. Kopiowanie **identyfikator jednostki SAML** i wkleić go do usługi Azure AD **identyfikator** polu tekstowym w **Tableau Server domena i adresy URL** sekcji.

    e. Kliknij przycisk **pobrać pliku metadanych XML** i otwórz go w aplikacji edytora tekstu. Znajdź adres URL usługi konsumenta potwierdzenia z żądania Http Post i indeks 0 i skopiuj adres URL. Teraz wklej go do usługi Azure AD **adres URL odpowiedzi** polu tekstowym w **Tableau Server domena i adresy URL** sekcji.

    f. Zlokalizuj plik metadanych Federacji, pobrany z witryny Azure portal, a następnie przekaż go w **plik metadanych SAML dostawcy tożsamości**.

    g. Wprowadź nazwę dla atrybutów, których dostawca tożsamości używa do przechowywania nazw użytkowników, nazw wyświetlanych i adresów e-mail.

    h. Kliknij pozycję **Zapisz**

    >[!NOTE] 
    >Klient musi przekazać dowolny certyfikat w konfiguracji logowania jednokrotnego SAML serwer firmy Tableau i będą Pobierz ignorowane w przepływie logowania jednokrotnego.
    >Jeśli potrzebujesz pomoc dotyczącą konfigurowania SAML na serwer firmy Tableau można znaleźć w tym artykule [skonfigurować SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create-aaduser-01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create-aaduser-02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
  
### <a name="create-tableau-server-test-user"></a>Tworzenie użytkownika testowego Tableau Server

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon Tableau Server. Należy aprowizować wszystkich użytkowników na serwerze Tableau. 

Że nazwa użytkownika powinna odpowiadać wartości, które zostały skonfigurowane w atrybucie niestandardowym usługi Azure AD z **username**. Za pomocą mapowania poprawne integracji powinny działać [usługi Azure AD Konfigurowanie logowania jednokrotnego](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika, należy skontaktować się z administratorem Tableau Server w Twojej organizacji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do serwera Tableau.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Tableau Server**.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Tableau Server w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Tableau Server.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
