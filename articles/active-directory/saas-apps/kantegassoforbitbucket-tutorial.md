---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kantega Usługa rejestracji Jednokrotnej dla Bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7f24c211e057447782c7fab596c9be73106c552
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60265772"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket

W tym samouczku dowiesz się, jak zintegrować Kantega Usługa rejestracji Jednokrotnej dla Bitbucket za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie Kantega Usługa rejestracji Jednokrotnej dla Bitbucket z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Kantega Bitbucket
- Użytkowników, aby automatycznie uzyskać zalogowanych do Kantega Usługa rejestracji Jednokrotnej dla Bitbucket (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługa rejestracji Jednokrotnej Kantega dla Bitbucket logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Kantega Usługa rejestracji Jednokrotnej dla Bitbucket z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Dodawanie Kantega Usługa rejestracji Jednokrotnej dla Bitbucket z galerii
Aby skonfigurować integrację Kantega Usługa rejestracji Jednokrotnej dla Bitbucket w usłudze Azure AD, należy dodać Kantega Usługa rejestracji Jednokrotnej dla Bitbucket z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Kantega Usługa rejestracji Jednokrotnej dla Bitbucket w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

1. W panelu wyników wybierz **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, dla Bitbucket, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Kantega Usługa rejestracji Jednokrotnej dla Bitbucket dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Kantega Usługa rejestracji Jednokrotnej dla Bitbucket musi zostać nawiązane.

W Kantega Usługa rejestracji Jednokrotnej dla Bitbucket, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego Bitbucket](#creating-a-kantega-sso-for-bitbucket-test-user)**  — aby odpowiednikiem Britta Simon w Kantega Usługa rejestracji Jednokrotnej dla Bitbucket, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w Twojej rejestracji Jednokrotnej Kantega, Bitbucket aplikacji.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

1. W **tożsamości** zainicjować tryb, w **Kantega logowania jednokrotnego, Bitbucket, domena i adresy URL** sekcji wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. W **SP** inicjowane trybu wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości są odbierane podczas konfigurowania wtyczki Bitbucket, które zostało wyjaśnione w dalszej części tego samouczka.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do portalu administratora usługi Bitbucket.

1. Kliknij przycisk koła zębatego, a następnie kliknij przycisk **Znajdź nowe dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Wyszukiwanie **Kantega Usługa rejestracji Jednokrotnej Bitbucket SAML i protokołu Kerberos** i kliknij przycisk **zainstalować** przycisk, aby zainstalować nowe wtyczki SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Rozpocznie się instalacja dodatku plug-in.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon33.png)

1.  Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
1. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę. 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. W **SAML** sekcji. Wybierz **usługi Azure Active Directory (Azure AD)** z **dostawcy tożsamości Dodaj** listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowe**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Na **właściwości aplikacji** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopiuj **identyfikator URI Identyfikatora aplikacji** wartości i używać go jako **identyfikator, adres URL odpowiedzi i adres URL logowania** na **Kantega logowania jednokrotnego, Bitbucket, domena i adresy URL** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **Importuj metadane** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Wybierz **plik metadanych na moim komputerze**i przekazywania pliku metadanych, który został pobrany z witryny Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **nazwę i logowania jednokrotnego lokalizację** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w **nazwę dostawcy tożsamości** (np. usługi Azure AD) w polu tekstowym.

    b. Kliknij przycisk **Dalej**.

1. Zweryfikuj certyfikat podpisywania, a następnie kliknij przycisk **dalej**.   

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Na **kont użytkowników Bitbucket** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Wybierz **tworzenie użytkowników w firmy Bitbucket wewnętrznej katalogu, w razie potrzeby** i wprowadź nazwę odpowiedniej grupy użytkowników (może być wiele nie. grup rozdzielone przecinkami).

    b. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Na **znane domen dla usługi Azure AD** sekcję, wykonać następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Wybierz **znane domen** z lewego panelu strony.

    b. Wprowadź nazwę domeny w **znane domen** pola tekstowego.

    c. Kliknij pozycję **Zapisz**.  

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego Bitbucket

Aby umożliwić użytkownikom usługi Azure AD do logowania się w usłudze Bitbucket, musi być przygotowana do Bitbucket. W Kantega Usługa rejestracji Jednokrotnej dla Bitbucket Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Bitbucket jako administrator.

1. Kliknij ikonę ustawienia.

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. W obszarze **administracji** kliknij sekcję pozycję **użytkowników**.

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Kliknij pozycję **Create user** (Utwórz użytkownika).

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Na **Create User** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.
    
    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.
    
    c. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.  

    e. W **Potwierdź hasło** polu tekstowym Wprowadź ponownie hasło użytkownika.

    f. Kliknij pozycję **Create user** (Utwórz użytkownika).   

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Kantega Usługa rejestracji Jednokrotnej dla Bitbucket.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Kantega Usługa rejestracji Jednokrotnej dla Bitbucket, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu przycisku logowania jednokrotnego Kantega kafelka Bitbucket w panelu dostępu możesz powinien pobrać automatycznie zalogowanych do Twojej rejestracji Jednokrotnej Kantega Bitbucket aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

