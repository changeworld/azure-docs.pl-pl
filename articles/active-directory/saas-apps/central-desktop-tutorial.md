---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą programu Desktop centralnej | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i centralnym pulpicie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d389a9675f7d4de56a03026c0d392b9988f6b0da
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201687"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu centralnej pulpitu

W tym samouczku dowiesz się, jak zintegrować centralnej pulpitu za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie pulpitu centralnej z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do pulpitu centralnej.
- Aby umożliwić użytkownikom automatycznie pobrać zalogowany do centralnej pulpitu za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej szczegółów na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu centralnej pulpitu, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Centralnym pulpicie pojedynczej logowania na włączona subskrypcji

> [!NOTE]
> Nie zaleca się testowanie kroki opisane w tym samouczku przy użyciu środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz jeszcze środowiska wersji próbnej usługi Azure AD [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie centralnej pulpitu za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-central-desktop-from-the-gallery"></a>Dodawanie centralnej pulpitu za pomocą galerii
Aby skonfigurować integrację programu Desktop centralnej w usłudze Azure AD, należy dodać centralną pulpitu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać centralną pulpitu z galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw][2]
    
1. Aby dodać nowe aplikacje, wybierz **nową aplikację** przycisk u góry okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **centralnym pulpicie**. Wybierz **centralnym pulpicie** z panel wyników, a następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Centralnym pulpicie na liście wyników](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu centralnej pulpitu oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, który użytkownik odpowiednika w centralnym pulpicie użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w centralnym pulpicie.

W centralnym pulpicie **Username** taką samą wartość jak **nazwa_użytkownika** w usłudze Azure AD. Łącze między dwóch użytkowników ma ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu centralnej pulpitu, należy wykonać poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
1. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. [Tworzenie użytkownika testowego centralnym pulpicie](#create-a-central-desktop-test-user) odpowiednikiem Britta Simon znajdują się w centralnej Desktop, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. [Testowanie logowania jednokrotnego](#test-single-sign-on) można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Desktop centralnej.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu centralnej pulpitu, wykonaj następujące czynności:**

1. W witrynie Azure portal na **centralnym pulpicie** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** dialogowym **tryb** listy rozwijanej wybierz **opartej na SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

1. W **centralnym pulpicie domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Centralnym pulpicie domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. W **adres URL logowania** wpisz adres URL z następującym wzorcem: `https://<companyname>.centraldesktop.com`

    b. W **identyfikator** wpisz adres URL z następującym wzorcem:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. W **adres URL odpowiedzi** wpisz adres URL z następującym wzorcem: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizować te wartości z identyfikatorem rzeczywisty adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta pulpitu centralnej](https://imeetcentral.com/contact-us) do uzyskania tych wartości. 

1. W **certyfikat podpisywania SAML** zaznacz **certyfikatu**. Następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

1. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/central-desktop-tutorial/tutorial_general_400.png)
    
1. W **centralnej konfiguracji pulpitu** zaznacz **skonfigurować centralny pulpitu** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki** sekcji.

    ![Centralna konfiguracja pulpitu](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

1. Zaloguj się do Twojej **centralnym pulpicie** dzierżawy.

1. Przejdź do **ustawienia**. Wybierz **zaawansowane**, a następnie wybierz pozycję **Single Sign On**.

    ![Instalacja — zaawansowana](./media/central-desktop-tutorial/ic769563.png "Instalacja — zaawansowana")

1. Na **pojedynczy znak na ustawienia** strony, wykonaj następujące czynności:

    ![Pojedynczy ustawień logowania jednokrotnego](./media/central-desktop-tutorial/ic769564.png "pojedynczy znak na ustawienia")
    
    a. Wybierz **Włącz SAML w wersji 2 funkcji logowania jednokrotnego**.
    
    b. W **adres URL logowania jednokrotnego** pole, Wklej **identyfikator jednostki SAML** wartością skopiowaną z witryny Azure portal.
    
    c. W **adres URL logowania SSO** pole, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartością skopiowaną z witryny Azure portal.
    
    d. W **adres URL wylogowania logowania jednokrotnego** pole, Wklej **adres URL wylogowania** wartością skopiowaną z witryny Azure portal.

1. W **metody weryfikacji podpisu wiadomości** sekcji, wykonaj następujące czynności:

    ![Komunikat o metodę weryfikacji podpisu](./media/central-desktop-tutorial/ic769565.png "metody weryfikacji podpisu wiadomości") . Wybierz pozycję **Certyfikat**.
    
    b. W **certyfikatu logowania jednokrotnego** listy wybierz **RSH SHA256**.
    
    c. Otwórz pobrany certyfikat w Notatniku. Następnie skopiuj zawartość certyfikatu i wklej go do **certyfikatu logowania jednokrotnego** pola.
        
    d. Wybierz **wyświetlone łącze do strony logowania SAMLv2**.
    
    e. Wybierz **aktualizacji**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę, a następnie przejść osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk Azure Active Directory](./media/central-desktop-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**. Następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/central-desktop-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/central-desktop-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-central-desktop-test-user"></a>Tworzenie użytkownika testowego centralnej pulpitu

Dla użytkowników usługi Azure AD można było się zalogować musi być obsługiwana w aplikacji Desktop centralnej. W tej sekcji opisano sposób tworzenia kont użytkowników usługi Azure AD w centralnym pulpicie.

> [!NOTE]
> Aby udostępnić konta użytkowników usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika pulpitu centralnej lub interfejsów API, które są dostarczane przez centralną pulpitu.

**Aprowizuj konta użytkownika do centralnej pulpitu:**

1. Zaloguj się do dzierżawy centralnej pulpitu.

1. Przejdź do **osób** > **wewnętrznych składowych**.

1. Wybierz **Dodaj wewnętrznych składowych**.

    ![Osoby](./media/central-desktop-tutorial/ic781051.png "Osoby")
    
1. W **wiadomości E-mail adres dla nowych elementów członkowskich** wpisz konto usługi Azure AD, którą chcesz aprowizować, a następnie wybierz **dalej**.

    ![Adresy nowych elementów członkowskich e-mail](./media/central-desktop-tutorial/ic781052.png "adresy nowych elementów członkowskich E-mail")

1. Wybierz **Dodaj wewnętrzne elementy członkowskie**.

    ![Dodawanie elementu członkowskiego wewnętrznego](./media/central-desktop-tutorial/ic781053.png "Dodawanie wewnętrznego elementu członkowskiego")
   
   >[!NOTE]
   >Użytkownicy, dodani otrzymywać wiadomość e-mail zawierającą link do potwierdzenia do aktywacji ich kont.
   
### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do nadawania im praw do centralnej pulpitu za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać centralne pulpitu Britta Simon, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji. Przejdź do widoku katalogu, a następnie przejdź do **aplikacje dla przedsiębiorstw**.

1. Wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **centralnym pulpicie**.

    ![Link centralnej pulpitu na liście aplikacji](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

1. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”][202]

1. Wybierz przycisk **Add** (Dodaj). Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![Okienko Dodawanie przypisania][203]

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** w **użytkowników** listy.

1. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

1. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka pulpitu centralnej w panelu dostępu, automatycznie pobrać zarejestrowaniu w usłudze aplikacji pulpitu centralnej.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

