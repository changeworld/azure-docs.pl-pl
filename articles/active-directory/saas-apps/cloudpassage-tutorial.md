---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą CloudPassage | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 10ef9e0c07f6bad393fdb62de85456483284a998
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821315"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą CloudPassage

W tym samouczku dowiesz się, jak zintegrować CloudPassage w usłudze Azure Active Directory (Azure AD).

Integrowanie CloudPassage z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do CloudPassage
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do CloudPassage (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą CloudPassage, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- CloudPassage logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie CloudPassage z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-cloudpassage-from-the-gallery"></a>Dodawanie CloudPassage z galerii
Aby skonfigurować integrację CloudPassage w usłudze Azure AD, należy dodać CloudPassage z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać CloudPassage z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **CloudPassage**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cloudpassage-tutorial/tutorial_cloudpassage_search.png)

1. W panelu wyników wybierz **CloudPassage**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą CloudPassage w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w CloudPassage do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w CloudPassage musi można ustanowić.

W CloudPassage, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą CloudPassage, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego CloudPassage](#creating-a-cloudpassage-test-user)**  — aby odpowiednikiem Britta Simon w CloudPassage połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji CloudPassage.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z CloudPassage, wykonaj następujące czynności:**

1. W witrynie Azure portal na **CloudPassage** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

1. Na **CloudPassage domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://portal.cloudpassage.com/saml/init/accountid`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://portal.cloudpassage.com/saml/consume/accountid`. Można uzyskać wartość tego atrybutu, klikając **dokumentacji konfiguracji logowania jednokrotnego** w **ustawienia rejestracji jednokrotnej** sekcji CloudPassage witryny Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta CloudPassage](https://www.cloudpassage.com/company/contact/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

1. Aplikacja CloudPassage oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego działania.
   
   ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji CloudPassage** , kliknij przycisk **skonfigurować CloudPassage** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

1. W oknie innej przeglądarki Zaloguj się do witryny firmy CloudPassage jako administrator.

1. W menu u góry kliknij **ustawienia**, a następnie kliknij przycisk **administrowania lokacją**. 
   
    ![Konfigurowanie logowania jednokrotnego][12]

1. Kliknij przycisk **ustawienia uwierzytelniania** kartę. 
   
    ![Konfigurowanie logowania jednokrotnego][13]

1. W **ustawienia rejestracji jednokrotnej** sekcji, wykonaj następujące czynności: 
   
    ![Konfigurowanie logowania jednokrotnego][14]

    a. Wybierz **Włącz sign-on(SSO) (dokumentacja ustawień logowania jednokrotnego)** pola wyboru.
    
    b. Wklej **identyfikator jednostki SAML** do **adres URL wystawcy SAML** pola tekstowego.
  
    c. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do **adresu URL punktu końcowego protokołu SAML** pola tekstowego.
  
    d. Wklej **adres URL wylogowania** do **strona docelowa wylogowania** pola tekstowego.
  
    e. Otwórz pobrany certyfikat w programie Notatnik, skopiuj zawartość pobranego certyfikatu do Schowka, a następnie wklej go do **x 509 certyfikatu** pola tekstowego.
  
    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cloudpassage-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cloudpassage-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cloudpassage-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/cloudpassage-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Tworzenie użytkownika testowego CloudPassage

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w CloudPassage.

**Aby utworzyć użytkownika o nazwie Britta Simon w CloudPassage, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **CloudPassage** witryny firmy jako administrator. 

1. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie kliknij przycisk **administrowania lokacją**. 
   
   ![Tworzenie użytkownika testowego CloudPassage][22] 

1. Kliknij przycisk **użytkowników** kartę, a następnie kliknij przycisk **Dodaj nowego użytkownika**. 
   
   ![Tworzenie użytkownika testowego CloudPassage][23]

1. W **Dodaj nowego użytkownika** sekcji, wykonaj następujące czynności: 
   
   ![Tworzenie użytkownika testowego CloudPassage][24]
    
    a. W **imię** polu tekstowym wpisz Britta. 
  
    b. W **nazwisko** polu tekstowym wpisz Simon.
  
    c. W **Username** pola tekstowego, **wiadomości E-mail** pole tekstowe i **wpisz ponownie adres E-mail** polu tekstowym wpisz nazwę użytkownika Britty w usłudze Azure AD.
  
    d. Jako **typ dostępu**, wybierz opcję **Włącz dostęp do portalu Halo**.
  
    e. Kliknij pozycję **Add** (Dodaj).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do CloudPassage.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon CloudPassage, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **CloudPassage**.

    ![Konfigurowanie logowania jednokrotnego](./media/cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka CloudPassage w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji CloudPassage.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/cloudpassage-tutorial/tutorial_general_203.png

