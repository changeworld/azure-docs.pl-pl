---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem HR Cezanne | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Cezanne HR oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 28a699f62a97120a27ba4d6f555182f090249dcf
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812308"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem HR Cezanne

W tym samouczku dowiesz się, jak zintegrować Cezanne HR oprogramowania z usługi Azure Active Directory (Azure AD).

Integracja oprogramowania HR Cezanne z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do oprogramowania HR Cezanne.
- Użytkowników, aby automatycznie uzyskać zalogowanych do oprogramowania HR Cezanne (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu oprogramowania HR Cezanne, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Oprogramowanie HR Cezanne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania HR Cezanne z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Dodawanie oprogramowania HR Cezanne z galerii
Aby skonfigurować integrację Cezanne HR oprogramowania w usłudze Azure AD, należy dodać Cezanne HR oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Cezanne HR oprogramowania z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Cezanne HR oprogramowania**, wybierz opcję **Cezanne HR oprogramowania** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Oprogramowanie HR Cezanne na liście wyników](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą oprogramowania HR Cezanne oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowaniu HR Cezanne do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu HR Cezanne musi zostać ustanowione.

W oprogramowaniu HR Cezanne, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania HR Cezanne, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **Tworzenie użytkownika testowego oprogramowania HR Cezanne** — aby odpowiednikiem Britta Simon w Cezanne HR oprogramowanie, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Cezanne HR oprogramowania.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania HR Cezanne, wykonaj następujące czynności:**

1. W witrynie Azure portal na **oprogramowania HR Cezanne** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

1. Na **Cezanne HR oprogramowania domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Cezanne HR oprogramowania domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Cezanne HR oprogramowanie klienckie](https://cezannehr.com/services/support/) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

1. Na **konfiguracji oprogramowania HR Cezanne** , kliknij przycisk **Konfigurowanie oprogramowania HR Cezanne** otworzyć **Konfigurowanie logowania jednokrotnego** okna.

    ![Konfiguracja oprogramowania Cezanne HR](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

1. Przewiń w dół do **krótki** sekcji. Kopiuj **SAML pojedynczego logowania jednokrotnego adresu URL usługi i identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfiguracja oprogramowania Cezanne HR](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

1. W oknie przeglądarki internetowej innej logowanie jednokrotne do swojej dzierżawy Cezanne HR oprogramowania jako administrator.

1. W okienku nawigacji po lewej stronie kliknij **konfiguracji systemu**. Przejdź do **ustawienia zabezpieczeń**. Następnie przejdź do **konfiguracji rejestracji jednokrotnej**.

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

1. W **Zezwalaj użytkownikom na zalogowanie się przy użyciu następującej usługi pojedynczego logowania jednokrotnego (SSO)** panelu wyboru **SAML 2.0** pole, a następnie wybierz **Advanced Configuration** opcji.

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

1. Kliknij przycisk **Dodaj nowe** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

1. Wykonaj następujące czynności na **dostawców tożsamości w wersji 2.0 SAML** sekcji.

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Wprowadź nazwę dostawcy tożsamości jako **nazwę wyświetlaną**.

    b. W **identyfikator jednostki** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal. 

    c. Zmiana **powiązania SAML** do "POST".

    d. W **punktu końcowego usługi tokenu zabezpieczającego** pola tekstowego, Wklej wartość **pojedynczego SAML logowania jednokrotnego adres URL usługi** skopiowanej w witrynie Azure portal.

    e. W polu tekstowym Nazwa atrybutu identyfikator użytkownika, wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Kliknij przycisk **przekazywanie** ikonę, aby przekazać certyfikat pobrany z witryny Azure portal.
    
    g. Kliknij przycisk **OK**. 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie pojedynczego logowania jednokrotnego w aplikacji po stronie](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Tworzenie użytkownika testowego Cezanne HR oprogramowania

Aby umożliwić użytkownikom usługi Azure AD zalogować się do oprogramowania HR Cezanne, musi być obsługiwana w Cezanne HR oprogramowania. W przypadku oprogramowania HR Cezanne Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1.  Zaloguj się do witryny firmy Cezanne HR oprogramowania jako administrator.

1.  W okienku nawigacji po lewej stronie kliknij **konfiguracji systemu**. Przejdź do **Zarządzanie użytkownikami**. Następnie przejdź do **Dodaj nowego użytkownika**.

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nowy użytkownik")

1.  Na **dane osobowe** sekcji, wykonaj następujące czynności:

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nowy użytkownik")
    
    a. Ustaw **użytkownik wewnętrzny** jako wyłączone.
    
    b. W **imię** polu tekstowym wpisz imię użytkownika, takie jak **Britta**.  
 
    c. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takie jak **Simon**.
    
    d. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

1.  Na **informacje o koncie** sekcji, wykonaj następujące czynności:

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nowy użytkownik")
    
    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.
    
    b. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.
    
    c. Wybierz **HR Professional** jako **roli zabezpieczeń**.
    
    d. Kliknij przycisk **OK**.

1. Przejdź do **logowania jednokrotnego** kartę, a następnie wybierz pozycję **Dodaj nowe** w **SAML 2.0 identyfikatory** obszaru.

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Użytkownik")

1. Wybierz dostawcę tożsamości dla **dostawcy tożsamości** i w polu tekstowym **identyfikator użytkownika**, wprowadź adres e-mail konta Britta Simon.

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Użytkownik")
    
1. Kliknij przycisk **Save** (Zapisz).

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Użytkownik")

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania HR Cezanne za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Cezanne HR oprogramowania, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **oprogramowania HR Cezanne**.

    ![Link Cezanne HR oprogramowania na liście aplikacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Cezanne HR oprogramowania w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do HR Cezanne w aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

