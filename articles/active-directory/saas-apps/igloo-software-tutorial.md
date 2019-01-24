---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Igloo | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Igloo oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: daa00db39ac55cd14b7720b534313407a98dde2c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817745"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Igloo

W tym samouczku dowiesz się, jak zintegrować Igloo oprogramowania z usługi Azure Active Directory (Azure AD).

Integracja oprogramowania Igloo z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do oprogramowania Igloo
- Użytkowników, aby automatycznie uzyskać zalogowanych do oprogramowania Igloo (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu oprogramowania Igloo, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Oprogramowanie Igloo logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania Igloo z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-igloo-software-from-the-gallery"></a>Dodawanie oprogramowania Igloo z galerii
Aby skonfigurować integrację Igloo oprogramowania w usłudze Azure AD, należy dodać Igloo oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Igloo oprogramowania z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **oprogramowania Igloo**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

1. W panelu wyników wybierz **oprogramowania Igloo**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą oprogramowania Igloo w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowaniu Igloo do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu Igloo musi zostać ustanowione.

W oprogramowaniu Igloo, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania Igloo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego oprogramowania Igloo](#creating-an-igloo-software-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowaniu Igloo, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Igloo oprogramowania.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania Igloo, wykonaj następujące czynności:**

1. W witrynie Azure portal na **oprogramowania Igloo** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

1. Na **Igloo oprogramowania domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.igloocommmunities.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Igloo oprogramowanie klienckie](https://www.igloosoftware.com/services/support) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji oprogramowania Igloo** , kliknij przycisk **Konfigurowanie oprogramowania Igloo** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Igloo oprogramowania.

1. Przejdź do **Panel sterowania**.
   
     ![Panel sterowania](./media/igloo-software-tutorial/ic799949.png "Panel sterowania")

1. W **członkostwa** kliknij pozycję **logowania ustawień**.
   
    ![Ustawienia logowania](./media/igloo-software-tutorial/ic783968.png "ustawienia logowania")

1. W sekcji Konfiguracja SAML kliknij **skonfigurować uwierzytelnianie SAML**.
   
    ![Konfiguracja SAML](./media/igloo-software-tutorial/ic783969.png "Konfiguracja SAML")
   
1. W **Konfiguracja ogólna** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja ogólna](./media/igloo-software-tutorial/ic783970.png "Konfiguracja ogólna")

    a. W **nazwa połączenia** polu tekstowym wpisz nazwę niestandardową dla danej konfiguracji.
   
    b. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    c. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
    
    d. Wybierz **odpowiedzi wylogowania i żądania HTTP typu** jako **WPIS**.
   
    e. Otwórz swoje **base-64** zakodowanego certyfikatu w programie Notatnik pobrane z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu publicznego** pola tekstowego.
    
1. W **odpowiedzi i Konfiguracja uwierzytelniania**, wykonaj następujące czynności:
    
    ![Odpowiedź i konfiguracji uwierzytelniania](./media/igloo-software-tutorial/IC783971.png "odpowiedzi i konfiguracji uwierzytelniania")
  
      a. Jako **dostawcy tożsamości**, wybierz opcję **Microsoft ADFS**.
      
      b. Jako **typ identyfikatora**, wybierz opcję **adres E-mail**. 

      c. W **atrybut E-mail** polu tekstowym wpisz **emailaddress**.

      d. W **atrybut imię** polu tekstowym wpisz **givenname**.

      e. W **ostatniego atrybutu nazwy** polu tekstowym wpisz **nazwisko**.

1. Wykonaj poniższe kroki, aby ukończyć konfigurację:
    
    ![Tworzenie użytkownika na temat logowania się](./media/igloo-software-tutorial/IC783972.png "Tworzenie użytkownika na temat logowania się") 

     a. Jako **Tworzenie użytkownika na temat logowania się**, wybierz opcję **Utwórz nowego użytkownika w witrynie, gdy logują się oni**.

     b. Jako **ustawienia logowania**, wybierz opcję **SAML użyj przycisku na ekranie "Sign in"**.

     c. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-igloo-software-test-user"></a>Tworzenie użytkownika testowego Igloo oprogramowania

Brak elementu działania umożliwiające konfigurowanie aprowizacji użytkownika Igloo oprogramowania.  

Gdy przypisany użytkownik próbuje zalogować się do oprogramowania Igloo za pomocą panelu dostępu, oprogramowanie Igloo sprawdza, czy użytkownik istnieje.  Jeśli nie ma użytkownika konta dostępne jeszcze, są tworzone przez oprogramowanie Igloo.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania Igloo za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Igloo oprogramowania, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **oprogramowania Igloo**.

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Igloo oprogramowania w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji oprogramowania Igloo.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png

