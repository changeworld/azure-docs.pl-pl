---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bambus | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kantega Usługa rejestracji Jednokrotnej dla Bambus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ceab1293b5bd1fbae9088783651d0effa8c5a78a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449006"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bambus

W tym samouczku dowiesz się, jak zintegrować Kantega Usługa rejestracji Jednokrotnej dla Bambus za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie Kantega Usługa rejestracji Jednokrotnej dla Bambus z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Kantega Bambus
- Użytkowników, aby automatycznie uzyskać zalogowanych do Kantega Usługa rejestracji Jednokrotnej dla Bambus (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bambus, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Usługa rejestracji Jednokrotnej Kantega dla Bambus logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Kantega Usługa rejestracji Jednokrotnej dla Bambus z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Dodawanie Kantega Usługa rejestracji Jednokrotnej dla Bambus z galerii
Aby skonfigurować integrację Kantega Usługa rejestracji Jednokrotnej dla Bambus w usłudze Azure AD, należy dodać Kantega Usługa rejestracji Jednokrotnej dla Bambus z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Kantega Usługa rejestracji Jednokrotnej dla Bambus z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Kantega Usługa rejestracji Jednokrotnej dla Bambus**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_search.png)

1. W panelu wyników wybierz **Kantega Usługa rejestracji Jednokrotnej dla Bambus**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, dla Bambus, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Kantega Usługa rejestracji Jednokrotnej dla Bambus do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Kantega Usługa rejestracji Jednokrotnej dla Bambus musi zostać nawiązane.

W Kantega Usługa rejestracji Jednokrotnej dla Bambus, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bambus, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego Bambus](#creating-a-kantega-sso-for-bamboo-test-user)**  — aby odpowiednikiem Britta Simon w Kantega Usługa rejestracji Jednokrotnej dla Bambus połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w Usługa rejestracji Jednokrotnej w sieci Kantega Bambus aplikacji.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bambus, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Kantega Usługa rejestracji Jednokrotnej dla Bambus** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_samlbase.png)

1. W **tożsamości** zainicjować tryb, w **Kantega Usługa rejestracji Jednokrotnej Bambus domen i adresów URL** sekcji wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url1.png)
    
    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. W **SP** inicjowane trybu wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url2.png)
    
    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Te wartości są odbierane podczas konfigurowania wtyczki Bambus, które zostało wyjaśnione w dalszej części tego samouczka.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/tutorial_general_400.png)
    
1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do serwera lokalnego Bambus.

1. Umieść kursor na koła zębatego, a następnie kliknij przycisk **dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon1.png)

1. W sekcji Karta dodatki, kliknij przycisk **Znajdź nowe dodatki**. Wyszukiwanie **Kantega Usługa rejestracji Jednokrotnej dla Bambus (SAML & Kerberos)** i kliknij przycisk **zainstalować** przycisk, aby zainstalować nowe wtyczki SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon2.png)

1. Rozpocznie się instalacja dodatku plug-in.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon21.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon33.png)

1.  Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon34.png)
    
1. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki. 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon3.png)

1. W **SAML** sekcji. Wybierz **usługi Azure Active Directory (Azure AD)** z **dostawcy tożsamości Dodaj** listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowe**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon5.png)

1. Na **właściwości aplikacji** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Kopiuj **identyfikator URI Identyfikatora aplikacji** wartości i używać go jako **identyfikator, adres URL odpowiedzi i adres URL logowania** na **Kantega Usługa rejestracji Jednokrotnej Bambus domen i adresów URL** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **Importuj metadane** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Wybierz **plik metadanych na moim komputerze**i przekazywania pliku metadanych, który został pobrany z witryny Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **nazwę i logowania jednokrotnego lokalizację** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w **nazwę dostawcy tożsamości** (np. usługi Azure AD) w polu tekstowym.

    b. Kliknij przycisk **Dalej**.

1. Zweryfikuj certyfikat podpisywania, a następnie kliknij przycisk **dalej**.   

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon9.png)

1. Na **kont użytkowników Bambus** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. Wybierz **tworzenie użytkowników w katalogu wewnętrznego Bambus firmy, w razie potrzeby** i wprowadź nazwę odpowiedniej grupy użytkowników (może być wiele nie. grup rozdzielone przecinkami).

    b. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon11.png)

1. Na **znane domen dla usługi Azure AD** sekcję, wykonać następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. Wybierz **znane domen** z lewego panelu strony.

    b. Wprowadź nazwę domeny w **znane domen** pola tekstowego.

    c. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/kantegassoforbamboo-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-kantega-sso-for-bamboo-test-user"></a>Tworzenie Kantega Usługa rejestracji Jednokrotnej dla użytkownika testowego Bambus

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Bambus, musi być obsługiwana w Bambus. W Kantega Usługa rejestracji Jednokrotnej dla Bambus Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego Bambus jako administrator.

1. Umieść kursor na koła zębatego, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](./media/kantegassoforbamboo-tutorial/user1.png) 

1. Kliknij przycisk **użytkowników**. W obszarze **Dodaj użytkownika** sekcji, wykonaj czynności follwing:

    ![Dodawanie pracownika](./media/kantegassoforbamboo-tutorial/user2.png) 

    a. W **Username** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.
    
    b. W **hasło** pole tekstowe, wpisz hasło użytkownika.

    c. W **Potwierdź hasło** polu tekstowym Wprowadź ponownie hasło użytkownika.
    
    d. W **imię i nazwisko** pole tekstowe, wpisz pełną nazwę użytkownika, takich jak Britta Simon.
    
    e. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.
    
    f. Kliknij pozycję **Zapisz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Kantega Usługa rejestracji Jednokrotnej dla Bambus.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Kantega Usługa rejestracji Jednokrotnej dla Bambus, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Kantega Usługa rejestracji Jednokrotnej dla Bambus**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu przycisku logowania jednokrotnego Kantega Bambus kafelka w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowanych do usługi logowania jednokrotnego Kantega Bambus aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbamboo-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbamboo-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbamboo-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbamboo-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbamboo-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbamboo-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbamboo-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbamboo-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbamboo-tutorial/tutorial_general_203.png

