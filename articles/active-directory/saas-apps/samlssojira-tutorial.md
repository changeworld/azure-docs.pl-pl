---
title: 'Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 6ae8256f3485d49d42efeb2927a6838252a1aeee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442911"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH

W tym samouczku dowiesz się, jak zintegrować logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH w usłudze Azure Active Directory (Azure AD).

Integracja logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD za pomocą logowania jednokrotnego SAML dla programu Jira przy rozdzielczości GmbH, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Logowanie Jednokrotne SAML do programu Jira przez rozwiązania logowania jednokrotnego GmbH włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie logowania jednokrotnego SAML dla programu Jira przy rozdzielczości GmbH z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML dla programu Jira przy rozdzielczości GmbH z galerii
Aby skonfigurować integrację logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH w usłudze Azure AD, należy dodać logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML dla programu Jira za rozdzielczość GmbH z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samlssojira-tutorial/tutorial_samlssojira_search.png)

1. W panelu wyników wybierz **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla programu Jira według rozdzielczości, GmbH, zależnie od użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie odpowiednika użytkownika w logowania jednokrotnego SAML dla programu Jira według rozdzielczości GmbH jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika w logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH musi zostać ustanowione.

W logowania jednokrotnego SAML Jira przez rozwiązania GmbH, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie logowania jednokrotnego SAML, dla programu Jira przez użytkownika testowego GmbH rozpoznawania](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)**  — aby odpowiednikiem Britta Simon w logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w usługi logowania jednokrotnego SAML dla programu Jira rozpoznawania GmbH aplikacji.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH, wykonaj następujące czynności:**

1. W witrynie Azure portal na **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

1. Na **logowania jednokrotnego SAML, Jira, rozpoznawanie domeny GmbH i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołu pomocy technicznej logowania jednokrotnego SAML dla programu Jira przez rozwiązania klienta GmbH](https://www.resolution.de/go/support) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/tutorial_general_400.png)
    
1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **logowania jednokrotnego SAML dla programu Jira przez portal administracyjny GmbH rozpoznawania** jako administrator.

1. Umieść kursor na koła zębatego, a następnie kliknij przycisk **dodatki**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon1.png)

1. Nastąpi przekierowanie do strony dostępu administratora. Wprowadź **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon2.png)

1. W sekcji Karta dodatki, kliknij przycisk **Znajdź nowe dodatki**. Wyszukiwanie **SAML logowanie jednokrotne (SSO) dla programu JIRA** i kliknij przycisk **zainstalować** przycisk, aby zainstalować nowe wtyczki SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon7.png)

1. Rozpocznie się instalacja dodatku plug-in. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon8.png)

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon9.png)

1.  Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon10.png)
    
1. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon11.png)

1. Na **plik konfiguracji wtyczki SAML SingleSignOn** kliknij **Dodaj nowy dostawca tożsamości** przycisk, aby skonfigurować ustawienia dostawcy tożsamości.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon4.png)

1. Na **wybierz dostawcy tożsamości SAML** strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5a.png)
 
    a. Ustaw **usługi Azure AD** jako typ dostawcy tożsamości.
    
    b. Dodaj **nazwa** dostawcy tożsamości (np. usługi Azure AD).
    
    c. Dodaj **opis** dostawcy tożsamości (np. usługi Azure AD).
    
    d. Kliknij przycisk **Dalej**.
    
1. Na **konfiguracji dostawcy tożsamości** kliknij **dalej** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5b.png)

1. Na **Importuj metadane dostawcy tożsamości SAML** strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5c.png)

    a. Kliknij przycisk **Załaduj plik** przycisk, a następnie wybierz plik XML metadanych został pobrany w kroku 5.

    b. Kliknij przycisk **importu** przycisku.
    
    c. Poczekaj chwilę import zakończy się pomyślnie.
    
    d. Kliknij przycisk **dalej** przycisku.
    
1. Na **atrybutu nazwy użytkownika i transformacji** kliknij **dalej** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon5d.png)
    
1. Na **użytkownika tworzenia i aktualizowania** kliknij **z & apisz dalej** można zapisać ustawień.    
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6a.png)
    
1. Na **testowanie ustawień usługi** kliknij **pominąć test & ręcznie skonfigurować** Aby pominąć test użytkownika teraz. Odbędzie się w następnej sekcji i wymaga niektóre ustawienia w witrynie Azure portal. 
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6b.png)
    
1. Podczas odczytywania okna dialogowego apprearing **pomijanie testów oznacza, że...** , kliknij przycisk **OK**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/addon6c.png)

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samlssojira-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samlssojira-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samlssojira-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samlssojira-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Tworzenie logowania jednokrotnego SAML, dla programu Jira przez użytkownika testowego GmbH rozwiązania

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH, ich musi być obsługiwana do logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH.  
W przypadku logowania jednokrotnego SAML Jira przez rozwiązania GmbH Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do usługi logowania jednokrotnego SAML dla programu Jira przez witrynę firmy GmbH rozpoznawania jako administrator.

1. Umieść kursor na koła zębatego, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user1.png) 

1. Nastąpi przekierowanie do strony dostępu administratora, aby wprowadzić **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user2.png) 

1. W obszarze **Zarządzanie użytkownikami** kliknij sekcję pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user3.png) 

1. Na **"Utwórz nowego użytkownika"** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/samlssojira-tutorial/user4.png) 

    a. W **adres E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    b. W **imię i nazwisko** pole tekstowe, wpisz pełną nazwę użytkownika, takich jak Britta Simon.

    c. W **Username** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    d. W **hasło** pole tekstowe, wpisz hasło użytkownika.

    e. Kliknij przycisk **Utwórz użytkownika**.   

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon logowania jednokrotnego SAML dla programu Jira rozpoznawania GmbH, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH**.

    ![Konfigurowanie logowania jednokrotnego](./media/samlssojira-tutorial/tutorial_samlssojira_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Kliknięcie logowania jednokrotnego SAML dla programu Jira rozpoznawania GmbH kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do usługi logowania jednokrotnego SAML dla programu Jira przez rozwiązania GmbH aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/samlssojira-tutorial/tutorial_general_203.png

