---
title: 'Samouczek: Integracja usługi Azure Active Directory z pakietem BPM Questetra | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: cc2d88bfc6b8ce57cebc2e35e3a9f2e3b826e9cd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816776"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem BPM Questetra

W tym samouczku dowiesz się, jak zintegrować Questetra BPM Suite z usługą Azure Active Directory (Azure AD).

Integrowanie Questetra BPM Suite z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do zestawu BPM Questetra
- Użytkowników, aby automatycznie uzyskać zalogowanych do zestawu BPM Questetra (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem BPM Questetra, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Pakiet BPM Questetra logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj pakiet BPM Questetra z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Dodaj pakiet BPM Questetra z galerii
Aby skonfigurować integrację Questetra BPM Suite w usłudze Azure AD, należy dodać pakiet BPM Questetra z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pakiet BPM Questetra z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Questetra BPM Suite**, wybierz opcję **Questetra BPM Suite** z panelu wyników, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Dodaj z galerii](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą zestawu BPM Questetra oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w zestawie BPM Questetra do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w pakiecie BPM Questetra musi zostać nawiązane.

W zestawie BPM Questetra, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z pakietem BPM Questetra, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego zestawu BPM Questetra](#create-a-questetra-bpm-suite-test-user)**  — aby odpowiednikiem Britta Simon w Questetra BPM zestaw, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji pakietu BPM Questetra.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z pakietem BPM Questetra, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Questetra BPM Suite** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Logowanie na podstawie protokołu SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

1. Na **Questetra BPM Suite domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Sekcja Questetra BPM Suite domena i adresy URL](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Możesz uzyskać te wartości z **SP informacji** sekcji na swoje **Questetra BPM Suite** witryny firmy, co zostało wyjaśnione w dalszej części samouczka lub skontaktuj się z [Obsługa Questetra BPM pakietu klienta zespół](https://www.questetra.com/contact/). 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base 64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Sekcji certyfikat podpisywania SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Przycisk Zapisz](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

1. Na **konfiguracji pakietu BPM Questetra** , kliknij przycisk **skonfigurować pakiet BPM Questetra** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Sekcja konfiguracji pakietu BPM Questetra](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **Questetra BPM Suite** witryny firmy jako administrator.

1. W menu u góry kliknij **ustawień systemowych**. 
   
    ![Usługa Azure AD logowanie jednokrotne][10]

1. Aby otworzyć **SingleSignOnSAML** kliknij **logowania jednokrotnego (SAML)**. 
   
    ![Usługa Azure AD logowanie jednokrotne][11]

1. Na Twojej **Questetra BPM Suite** firmy witryny, w **informacji SP** sekcji, wykonaj następujące czynności:

    a. Kopiowanie **adres URL usługi ACS**, a następnie wklej go do **na adres URL logowania** polu tekstowym w **Questetra BPM Suite domena i adresy URL** sekcji z witryny Azure portal.
    
    b. Kopia **identyfikator jednostki**, a następnie wklej go do **identyfikator** polu tekstowym w **Questetra BPM Suite domena i adresy URL** sekcji z witryny Azure portal.

1. Na Twojej **Questetra BPM Suite** firmy witryny, należy wykonać następujące czynności: 
   
    ![Konfigurowanie logowania jednokrotnego][15]
   
    a. Wybierz **włączyć rejestrację jednokrotną**.
   
    b. W **identyfikator jednostki** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.
    
    c. W **adres URL logowania strony** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
    
    d. W **adres URL strony wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
    
    e. W **format identyfikatora NameID** polu tekstowym wpisz `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Otwórz swoje **Base-64** zakodowanego certyfikatu w programie Notatnik pobrane z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu weryfikacji** pola tekstowego. 

    g. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Tworzenie użytkownika testowego Questetra BPM pakietu

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon Questetra BPM pakietu.

**Aby utworzyć użytkownika o nazwie Britta Simon w zestawie BPM Questetra, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Questetra BPM Suite jako administrator.
1. Przejdź do **ustawień systemowych > listę użytkowników > Nowy użytkownik**. 
1. W oknie dialogowym Nowy użytkownik wykonaj następujące czynności: 
   
    ![Tworzenie użytkownika testowego][300] 
   
    a. W **nazwa** polu tekstowym wpisz **nazwa** użytkownika **britta.simon@contoso.com**.
   
    b. W **E-mail** polu tekstowym wpisz **e-mail** użytkownika **britta.simon@contoso.com**
   
    c. W **hasło** polu tekstowym wpisz **hasło** użytkownika.
    
    d. Kliknij przycisk **Dodaj nowego użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do zestawu BPM Questetra za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Questetra BPM pakietu, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Questetra BPM Suite**.

    ![Questetra BPM pakietu na liście aplikacji](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Questetra BPM Suite w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Questetra BPM pakietu aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

