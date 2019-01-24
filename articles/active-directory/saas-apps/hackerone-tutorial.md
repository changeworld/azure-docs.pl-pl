---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Hackerone | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Hackerone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: fe070505970516efcd4e2ae46dedff2792f95b08
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817201"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą HackerOne

W tym samouczku dowiesz się, jak zintegrować HackerOne w usłudze Azure Active Directory (Azure AD).

Integrowanie HackerOne z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do HackerOne
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do HackerOne (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą HackerOne, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- HackerOne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie HackerOne z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-hackerone-from-the-gallery"></a>Dodawanie HackerOne z galerii
Aby skonfigurować integrację HackerOne w usłudze Azure AD, należy dodać HackerOne z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać HackerOne z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **HackerOne**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hackerone-tutorial/tutorial_hackerone_search.png)

1. W panelu wyników wybierz **HackerOne**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą HackerOne w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w HackerOne do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w HackerOne musi można ustanowić.

W HackerOne, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą HackerOne, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego HackerOne](#creating-a-hackerone-test-user)**  — aby odpowiednikiem Britta Simon w HackerOne połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji HackerOne.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z HackerOne, wykonaj następujące czynności:**

1. W witrynie Azure portal na **HackerOne** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_samlbase.png)

1. Na **HackerOne pojedynczego logowania jednokrotnego adresu URL i identyfikator** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://hackerone.com/<company name>/authentication`

    b. W **identyfikator** pole tekstowe, wpisz adres URL jako:  `https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej HackerOne](mailto:support@hackerone.com) aby zyskać tę wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_general_400.png)

1. Na **konfiguracji HackerOne** , kliknij przycisk **skonfigurować HackerOne** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_configure.png) 

1. Logowanie w dzierżawie HackerOne jako administrator.

1. W menu u góry kliknij pozycję "**ustawienia**."
   
    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_001.png) 

1. Przejdź do "**uwierzytelniania**"i kliknij przycisk"**Dodaj ustawienia języka SAML**."
   
    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_003.png) 

1. Na **ustawienia języka SAML** okno dialogowe, należy wykonać następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_004.png) 

    a. W **domeny poczty E-mail** polu tekstowym wpisz zarejestrowanej domeny.

    b. W  **pojedynczy znak na adres URL** pola tekstowe, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. Otwórz swoje **plik certyfikatu** w Notatniku pobranego z witryny Azure portal, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu X509**  pola tekstowego.
    
    d. Kliknij pozycję **Zapisz**.

1. W oknie dialogowym Ustawienia uwierzytelniania wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Kliknij przycisk **Uruchom test**.

    b. Jeśli wartość **stan** pola jest równa **ostatnio stan testu: utworzone**, skontaktuj się z Twojego [HackerOne zespołem pomocy technicznej](mailto:support@hackerone.com) Aby zażądać przeglądu konfiguracji.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hackerone-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hackerone-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hackerone-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hackerone-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-hackerone-test-user"></a>Tworzenie użytkownika testowego HackerOne

Następnie należy utworzyć użytkownika o nazwie Britta Simon w HackerOne. HackerOne obsługę just-in-time, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Podczas uzyskiwania dostępu HackerOne nowego użytkownika jest tworzony, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika, należy skontaktować się z zespołem pomocy technicznej Certify. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do HackerOne.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon HackerOne, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **HackerOne**.

    ![Konfigurowanie logowania jednokrotnego](./media/hackerone-tutorial/tutorial_hackerone_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Na koniec należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  

Po kliknięciu kafelka HackerOne w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji HackerOne.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hackerone-tutorial/tutorial_general_01.png
[2]: ./media/hackerone-tutorial/tutorial_general_02.png
[3]: ./media/hackerone-tutorial/tutorial_general_03.png
[4]: ./media/hackerone-tutorial/tutorial_general_04.png

[100]: ./media/hackerone-tutorial/tutorial_general_100.png

[200]: ./media/hackerone-tutorial/tutorial_general_200.png
[201]: ./media/hackerone-tutorial/tutorial_general_201.png
[202]: ./media/hackerone-tutorial/tutorial_general_202.png
[203]: ./media/hackerone-tutorial/tutorial_general_203.png

