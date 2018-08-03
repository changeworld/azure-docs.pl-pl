---
title: 'Samouczek: Integracja usługi Azure Active Directory z TimeOffManager | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: e7f0f6bb778dedeea61b74b5ca0c2edbadd5279b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430291"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Samouczek: Integracja usługi Azure Active Directory z TimeOffManager

W tym samouczku dowiesz się, jak zintegrować TimeOffManager w usłudze Azure Active Directory (Azure AD).

Integrowanie TimeOffManager z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do TimeOffManager
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do TimeOffManager (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TimeOffManager, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- TimeOffManager logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj TimeOffManager z galerii
1. Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

## <a name="add-timeoffmanager-from-the-gallery"></a>Dodaj TimeOffManager z galerii
Aby skonfigurować integrację TimeOffManager w usłudze Azure AD, należy dodać TimeOffManager z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TimeOffManager z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **TimeOffManager**, wybierz opcję **TimeOffManager** panel wyników i kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Dodaj z galerii](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą TimeOffManager w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w TimeOffManager do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TimeOffManager musi można ustanowić.

W TimeOffManager, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TimeOffManager, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego TimeOffManager](#create-a-timeoffmanager-test-user)**  — aby odpowiednikiem Britta Simon w TimeOffManager połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji TimeOffManager.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TimeOffManager, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TimeOffManager** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![SAML logowania opartego na](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

1. Na **TimeOffManager domena i adresy URL** sekcji, wykonaj następujące czynności:

     ![Sekcja TimeOffManager domena i adresy URL](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywistego adresu URL odpowiedzi. Możesz uzyskać tę wartość z **logowania jednokrotnego na stronie ustawienia** co jest opisane w dalszej części samouczka lub skontaktuj się z [zespołem pomocy technicznej TimeOffManager](https://www.purelyhr.com/contact-us).
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Sekcji certyfikat podpisywania SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

1. Celem tej sekcji jest opisują, jak umożliwić użytkownikom na uwierzytelnianie TimeOffManger za pomocą konta w usłudze Azure AD przy użyciu Federacji, na podstawie protokołu SAML.
    
    Aplikacja TimeOffManger oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego.

    ![atrybuty tokenu języka SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "atrybuty tokenu języka saml")
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | Imię |User.givenName |
    | nazwisko |User.surname |
    | Email |User.mail |
    
    a.  Dla każdego wiersza danych w powyższej tabeli, kliknij przycisk **Dodaj atrybut użytkownika**.
    
    ![atrybuty tokenu języka SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "atrybuty tokenu języka saml")
    
    ![atrybuty tokenu języka SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "atrybuty tokenu języka saml")
    
    b.  W **nazwa atrybutu** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c.  W **wartość atrybutu** pola tekstowego, wybierz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d.  Kliknij przycisk **OK**.
    
1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/timeoffmanager-tutorial/tutorial_general_400.png)

1. Na **konfiguracji TimeOffManager** , kliknij przycisk **skonfigurować TimeOffManager** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Sekcja konfiguracji TimeOffManager](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy TimeOffManager, jako administrator.

1. Przejdź do **konta \> konta opcje \> pojedynczy ustawień logowania jednokrotnego**.
   
   ![Pojedynczy ustawień logowania jednokrotnego](./media/timeoffmanager-tutorial/ic795917.png "pojedynczy ustawień logowania jednokrotnego")
1. W **ustawienia rejestracji jednokrotnej** sekcji, wykonaj następujące czynności:
   
   ![Pojedynczy ustawień logowania jednokrotnego](./media/timeoffmanager-tutorial/ic795918.png "pojedynczy ustawień logowania jednokrotnego")
   
   a. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej cały certyfikat do **certyfikat X.509** pola tekstowego.
   
   b. W **wystawca dostawcy tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.
   
   c. W **adresu URL punktu końcowego protokołu IdP** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
   d. Jako **wymusić SAML**, wybierz opcję **nie**.
   
   e. Jako **automatyczne tworzenie użytkowników**, wybierz opcję **tak**.
   
   f. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
   
   g. Kliknij przycisk **Zapisz zmiany**.

1. W **funkcji logowania jednokrotnego ustawienia** stronie, skopiuj wartość **adres URL usługi konsumenta potwierdzenie** i wklej go w **adres URL odpowiedzi** polu tekstowym w obszarze **TimeOffManager Domena i adresy URL** sekcji w witrynie Azure portal. 

      ![Pojedynczy ustawień logowania jednokrotnego](./media/timeoffmanager-tutorial/ic795915.png "pojedynczy ustawień logowania jednokrotnego")

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/timeoffmanager-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Użytkownicy i grupy--> Wszyscy użytkownicy](./media/timeoffmanager-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Dodawanie przycisku](./media/timeoffmanager-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Strony okna dialogowego użytkownika](./media/timeoffmanager-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Tworzenie użytkownika testowego TimeOffManager

Aby umożliwić użytkownikom usługi Azure AD zalogować się do TimeOffManager, musi być obsługiwana na TimeOffManager.  

Obsługuje TimeOffManager dokładnie na czas Inicjowanie obsługi użytkowników. Brak elementu akcji dla Ciebie.  

Użytkownicy są dodawani automatycznie podczas pierwszego logowania przy użyciu logowania jednokrotnego na.

>[!NOTE]
>Można użyć jakichkolwiek innych TimeOffManager użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez TimeOffManager można uaktywniać ich konta usługi Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do TimeOffManager.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon TimeOffManager, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **TimeOffManager**.

    ![TimeOffManager listy aplikacji](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka TimeOffManager w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji TimeOffManager. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/timeoffmanager-tutorial/tutorial_general_203.png

