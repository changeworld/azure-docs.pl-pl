---
title: 'Samouczek: Integracja usługi Azure Active Directory z Samanage | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 118ab72c9afc13c5792f229f9c7bc61d226553d5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420578"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Samouczek: Integracja usługi Azure Active Directory z Samanage

W tym samouczku dowiesz się, jak zintegrować Samanage w usłudze Azure Active Directory (Azure AD).

Integrowanie Samanage z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Samanage
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Samanage (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Samanage, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Samanage logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Samanage z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-samanage-from-the-gallery"></a>Dodawanie Samanage z galerii
Aby skonfigurować integrację Samanage w usłudze Azure AD, należy dodać Samanage z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Samanage z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Samanage**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/tutorial_samanage_search.png)

1. W panelu wyników wybierz **Samanage**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Samanage w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Samanage do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Samanage musi można ustanowić.

W Samanage, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Samanage, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Samanage](#creating-a-samanage-test-user)**  — aby odpowiednikiem Britta Simon w Samanage połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Samanage.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Samanage, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Samanage** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/samanage-tutorial/tutorial_samanage_samlbase.png)

1. Na **Samanage domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/samanage-tutorial/tutorial_samanage_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka, należy zaktualizować te wartości. Aby uzyskać więcej informacji skontaktuj się z [zespołem pomocy technicznej klienta Samanage](https://www.samanage.com/support).    
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/samanage-tutorial/tutorial_samanage_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/samanage-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Samanage** , kliknij przycisk **skonfigurować Samanage** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/samanage-tutorial/tutorial_samanage_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Samanage, jako administrator.

1. Kliknij przycisk **pulpit nawigacyjny** i wybierz **Instalatora** w okienku nawigacji po lewej stronie.
   
    ![Pulpit nawigacyjny](./media/samanage-tutorial/tutorial_samanage_001.png "pulpitu nawigacyjnego")

1. Kliknij przycisk **logowanie jednokrotne**.
   
    ![Logowanie jednokrotne](./media/samanage-tutorial/tutorial_samanage_002.png "logowanie jednokrotne")

1. Przejdź do **logowania za pośrednictwem protokołu SAML** sekcji, wykonaj następujące czynności:
   
    ![Zaloguj się przy użyciu protokołu SAML](./media/samanage-tutorial/tutorial_samanage_003.png "logowania za pośrednictwem protokołu SAML")
 
    a. Kliknij przycisk **włączyć logowanie jednokrotne z SAML**.  
 
    b. W **adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.    
 
    c. Upewnij się, **adres URL logowania** odpowiada **na adres URL logowania** z **Samanage domena i adresy URL** sekcji w witrynie Azure portal.
 
    d. W **adres URL wylogowania** polu tekstowym wprowadź wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
 
    e. W **wystawcy SAML** pole tekstowe, wpisz identyfikator URI aplikacji ustaw w dostawcy tożsamości.
 
    f. Otwórz swój certyfikat zakodowany base-64, pobranego z witryny Azure portal w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **Wklej Twojego dostawcy tożsamości x.509 certyfikatu poniżej** pola tekstowego.
 
    g. Kliknij przycisk **tworzenie użytkowników, jeśli nie istnieją w Samanage**.
 
    h. Kliknij przycisk **aktualizacji**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-samanage-test-user"></a>Tworzenie użytkownika testowego Samanage

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Samanage, musi być obsługiwana w Samanage.  
W przypadku Samanage Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Samanage jako administrator.

1. Kliknij przycisk **pulpit nawigacyjny** i wybierz **Instalatora** w pan nawigacji po lewej stronie.
   
    ![Instalator](./media/samanage-tutorial/tutorial_samanage_001.png "instalacji")

1. Kliknij przycisk **użytkowników** kartę
   
    ![Użytkownicy](./media/samanage-tutorial/tutorial_samanage_006.png "użytkowników")

1. Kliknij przycisk **nowego użytkownika**.
   
    ![Nowy użytkownik](./media/samanage-tutorial/tutorial_samanage_007.png "nowego użytkownika")

1. Typ **nazwa** i **adres E-mail** konta usługi Azure Active Directory do obsługi administracyjnej, a następnie kliknij przycisk **Utwórz użytkownika**.
   
    ![Tworzenie użytkownika](./media/samanage-tutorial/tutorial_samanage_008.png "Tworzenie użytkownika")
   
   >[!NOTE]
   >Właściciel konta usługi Azure Active Directory będzie otrzymywać wiadomości e-mail i kliknij link, aby potwierdzić swoje konto, zanim stanie się aktywny. Można użyć jakichkolwiek innych Samanage użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Samanage do świadczenia usługi Azure Active Directory kont użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Samanage.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Samanage, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Samanage**.

    ![Konfigurowanie logowania jednokrotnego](./media/samanage-tutorial/tutorial_samanage_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Samanage w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Samanage.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samanage-tutorial/tutorial_general_01.png
[2]: ./media/samanage-tutorial/tutorial_general_02.png
[3]: ./media/samanage-tutorial/tutorial_general_03.png
[4]: ./media/samanage-tutorial/tutorial_general_04.png

[100]: ./media/samanage-tutorial/tutorial_general_100.png

[200]: ./media/samanage-tutorial/tutorial_general_200.png
[201]: ./media/samanage-tutorial/tutorial_general_201.png
[202]: ./media/samanage-tutorial/tutorial_general_202.png
[203]: ./media/samanage-tutorial/tutorial_general_203.png

