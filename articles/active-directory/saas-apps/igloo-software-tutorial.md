---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Igloo | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Igloo oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: acec9379661226dc53607aa9fd12197a193166e0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042412"
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

- Subskrypcję usługi Azure AD
- Oprogramowanie Igloo logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania Igloo z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-igloo-software-from-the-gallery"></a>Dodawanie oprogramowania Igloo z galerii
Aby skonfigurować integrację Igloo oprogramowania w usłudze Azure AD, należy dodać Igloo oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Igloo oprogramowania z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **oprogramowania Igloo**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. W panelu wyników wybierz **oprogramowania Igloo**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą oprogramowania Igloo w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowaniu Igloo do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu Igloo musi zostać ustanowione.

W oprogramowaniu Igloo, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania Igloo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego oprogramowania Igloo](#creating-an-igloo-software-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowaniu Igloo, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Igloo oprogramowania.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania Igloo, wykonaj następujące czynności:**

1. W witrynie Azure portal na **oprogramowania Igloo** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. Na **Igloo oprogramowania domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.igloocommmunities.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej Igloo oprogramowanie klienckie](https://www.igloosoftware.com/services/support) do uzyskania tych wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_general_400.png)
    
6. Na **konfiguracji oprogramowania Igloo** , kliknij przycisk **Konfigurowanie oprogramowania Igloo** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Igloo oprogramowania.

8. Przejdź do **Panel sterowania**.
   
     ![W Panelu sterowania](./media/igloo-software-tutorial/ic799949.png "w Panelu sterowania")

9. W **członkostwa** kliknij pozycję **logowania ustawień**.
   
    ![Ustawienia logowania](./media/igloo-software-tutorial/ic783968.png "ustawienia logowania")

10. W sekcji Konfiguracja SAML kliknij **skonfigurować uwierzytelnianie SAML**.
   
    ![Plik konfiguracji SAML](./media/igloo-software-tutorial/ic783969.png "plik konfiguracji SAML")
   
11. W **Konfiguracja ogólna** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja ogólna](./media/igloo-software-tutorial/ic783970.png "Konfiguracja ogólna")

    a. W **nazwa połączenia** polu tekstowym wpisz nazwę niestandardową dla danej konfiguracji.
   
    b. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    c. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
    
    d. Wybierz **odpowiedzi wylogowania i żądania HTTP typu** jako **WPIS**.
   
    e. Otwórz swoje **base-64** zakodowanego certyfikatu w programie Notatnik pobrane z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu publicznego** pola tekstowego.
    
12. W **odpowiedzi i Konfiguracja uwierzytelniania**, wykonaj następujące czynności:
    
    ![Odpowiedź i konfiguracji uwierzytelniania](./media/igloo-software-tutorial/IC783971.png "odpowiedzi i konfiguracji uwierzytelniania")
  
      a. Jako **dostawcy tożsamości**, wybierz opcję **Microsoft ADFS**.
      
      b. Jako **typ identyfikatora**, wybierz opcję **adres E-mail**. 

      c. W **atrybut E-mail** polu tekstowym wpisz **emailaddress**.

      d. W **atrybut imię** polu tekstowym wpisz **givenname**.

      e. W **ostatniego atrybutu nazwy** polu tekstowym wpisz **nazwisko**.

13. Wykonaj poniższe kroki, aby ukończyć konfigurację:
    
    ![Tworzenie użytkownika na temat logowania się](./media/igloo-software-tutorial/IC783972.png "Tworzenie użytkownika na temat logowania się") 

     a. Jako **Tworzenie użytkownika na temat logowania się**, wybierz opcję **Utwórz nowego użytkownika w witrynie, gdy logują się oni**.

     b. Jako **ustawienia logowania**, wybierz opcję **SAML użyj przycisku na ekranie "Sign in"**.

     c. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-igloo-software-test-user"></a>Tworzenie użytkownika testowego Igloo oprogramowania

Brak elementu działania umożliwiające konfigurowanie aprowizacji użytkownika Igloo oprogramowania.  

Gdy przypisany użytkownik próbuje zalogować się do oprogramowania Igloo za pomocą panelu dostępu, oprogramowanie Igloo sprawdza, czy użytkownik istnieje.  Jeśli nie ma użytkownika konta dostępne jeszcze, są tworzone przez oprogramowanie Igloo.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania Igloo za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Igloo oprogramowania, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **oprogramowania Igloo**.

    ![Konfigurowanie logowania jednokrotnego](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Igloo oprogramowania w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji oprogramowania Igloo.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

