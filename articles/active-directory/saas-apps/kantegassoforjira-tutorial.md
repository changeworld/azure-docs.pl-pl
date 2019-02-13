---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, dla programu JIRA | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kantega Usługa rejestracji Jednokrotnej dla programu JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74fbdd2eb2d53d4ae7ed164a8bd097b6c02714a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200840"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, dla programu JIRA

W tym samouczku dowiesz się, jak zintegrować Kantega Usługa rejestracji Jednokrotnej dla programu JIRA z usługą Azure Active Directory (Azure AD).

Integrowanie Kantega Usługa rejestracji Jednokrotnej dla programu JIRA z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Kantega JIRA
- Użytkowników, aby automatycznie uzyskać zalogowanych do Kantega Usługa rejestracji Jednokrotnej dla programu JIRA (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Kantega, dla programu JIRA, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługa rejestracji Jednokrotnej Kantega dla programu JIRA logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Kantega Usługa rejestracji Jednokrotnej dla programu JIRA z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Dodawanie Kantega Usługa rejestracji Jednokrotnej dla programu JIRA z galerii
Aby skonfigurować integrację Kantega Usługa rejestracji Jednokrotnej dla programu JIRA do usługi Azure AD, należy dodać Kantega Usługa rejestracji Jednokrotnej dla programu JIRA z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Kantega Usługa rejestracji Jednokrotnej dla programu JIRA z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Kantega Usługa rejestracji Jednokrotnej dla programu JIRA**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_search.png)

1. W panelu wyników wybierz **Kantega Usługa rejestracji Jednokrotnej dla programu JIRA**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, dla programu JIRA na podstawie użytkownika testowego, o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Kantega Usługa rejestracji Jednokrotnej dla programu JIRA do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Kantega Usługa rejestracji Jednokrotnej dla programu JIRA musi nawiązać.

W Kantega Usługa rejestracji Jednokrotnej dla programu JIRA, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, dla programu JIRA, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego programu JIRA](#creating-a-kantega-sso-for-jira-test-user)**  — aby odpowiednikiem Britta Simon w Kantega Usługa rejestracji Jednokrotnej dla programu JIRA, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w Usługa rejestracji Jednokrotnej w sieci Kantega dla aplikacji programu JIRA.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, dla programu JIRA, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Kantega Usługa rejestracji Jednokrotnej dla programu JIRA** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_samlbase.png)

1. W **dostawcy tożsamości** zainicjować tryb, w **Kantega Usługa rejestracji Jednokrotnej dla programu JIRA domena i adresy URL** sekcji wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_url1.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. W **SP** inicjowane trybu wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_url2.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości można uzyskać podczas konfigurowania wtyczki usługi Jira, co jest objaśnione w dalszej części tego samouczka.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/tutorial_general_400.png)
    
1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do swojej JIRA na serwerze lokalnym.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon1.png)

1. W sekcji karty Dodatki kliknij pozycję **Znajdź nowe dodatki**. Wyszukiwanie **Kantega Usługa rejestracji Jednokrotnej dla programu JIRA (SAML i protokołu Kerberos)** i kliknij przycisk **zainstalować** przycisk, aby zainstalować nowe wtyczki SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon2.png)

1. Rozpocznie się instalacja dodatku plug-in.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon3.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon33.png)

1.  Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Nowy dodatek plug-in znajduje się w obszarze **INTEGRACJE**. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon35.png)

1. W **SAML** sekcji. Wybierz **usługi Azure Active Directory (Azure AD)** z **dostawcy tożsamości Dodaj** listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowe**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon5.png)       

1. Na **właściwości aplikacji** sekcję, wykonać następujące kroki: 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon6.png)

    a. Kopiuj **identyfikator URI Identyfikatora aplikacji** wartości i używać go jako **identyfikator, adres URL odpowiedzi i adres URL logowania** na **Kantega Usługa rejestracji Jednokrotnej dla programu JIRA domena i adresy URL** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **Importuj metadane** sekcję, wykonać następujące kroki: 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon7.png)

    a. Wybierz **plik metadanych na moim komputerze**i przekazywania pliku metadanych, który został pobrany z witryny Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **nazwę i logowania jednokrotnego lokalizację** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon8.png)
    
    a. Dodaj nazwę dostawcy tożsamości w **nazwę dostawcy tożsamości** (np. usługi Azure AD) w polu tekstowym.

    b. Kliknij przycisk **Dalej**.

1. Zweryfikuj certyfikat podpisywania, a następnie kliknij przycisk **dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon9.png)

1. Na **kont użytkowników programu JIRA** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon10.png)

    a. Wybierz **tworzenie użytkowników w katalogu wewnętrznego programu JIRA firmy, w razie potrzeby** i wprowadź nazwę odpowiedniej grupy użytkowników (może być wiele nie. grup rozdzielone przecinkami).

    b. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.    

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon11.png)

1. Na **znane domen dla usługi Azure AD** sekcję, wykonać następujące kroki: 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/addon12.png)

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

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforjira-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforjira-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforjira-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforjira-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-kantega-sso-for-jira-test-user"></a>Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego programu JIRA

Aby umożliwić użytkownikom usługi Azure AD zalogować się do programu JIRA, musi być obsługiwana w usłudze JIRA. W Kantega Usługa rejestracji Jednokrotnej dla programu JIRA Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do lokalnego serwera JIRA jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Dodawanie pracownika](./media/kantegassoforjira-tutorial/user1.png) 

1. W obszarze **Zarządzanie użytkownikami** kliknij sekcję pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/kantegassoforjira-tutorial/user2.png) 

1. Na stronie okna dialogowego **„Create new user”** (Tworzenie nowego użytkownika) wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/kantegassoforjira-tutorial/user3.png) 

    a. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    c. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. Kliknij pozycję **Create user** (Utwórz użytkownika).   

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Kantega Usługa rejestracji Jednokrotnej dla programu JIRA.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Kantega Usługa rejestracji Jednokrotnej dla programu JIRA, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Kantega Usługa rejestracji Jednokrotnej dla programu JIRA**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforjira-tutorial/tutorial_kantegassoforjira_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu przycisku logowania jednokrotnego Kantega dla kafelków programu JIRA na panelu dostępu możesz należy pobrać automatycznie zalogowanych do Twojej rejestracji Jednokrotnej Kantega dla aplikacji programu JIRA.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kantegassoforjira-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforjira-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforjira-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforjira-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforjira-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforjira-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforjira-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforjira-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforjira-tutorial/tutorial_general_203.png

