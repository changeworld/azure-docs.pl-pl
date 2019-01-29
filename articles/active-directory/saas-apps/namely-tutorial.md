---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą mianowicie | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory, a mianowicie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 9c6cf70721169dfc35e49ec253d7aeb5deace794
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185312"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą:

W tym samouczku dowiesz się, jak to znaczy zintegrować z usługą Azure Active Directory (Azure AD).

To znaczy integracji z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, który ma dostęp do:
- Umożliwia użytkownikom automatycznie uzyskują zalogowanych do mianowicie (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować usługę Azure AD integracji Tobie, potrzebne są następujące:

- Subskrypcji usługi Azure AD
- To znaczy logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie mianowicie z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-namely-from-the-gallery"></a>Dodawanie mianowicie z galerii
Aby skonfigurować integrację z a mianowicie w usłudze Azure AD, należy dodać to znaczy, za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać to znaczy, w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **mianowicie**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/tutorial_namely_search.png)

1. W panelu wyników wybierz **mianowicie**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji, skonfiguruj i testowanie usługi Azure AD logowanie jednokrotne za pomocą mianowicie oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, co użytkownik odpowiednika w mianowicie jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w mianowicie musi można ustanowić.

To znaczy, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne Tobie, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie mianowicie testowe użytkownika](#creating-a-namely-test-user)**  — aby odpowiednikiem Britta Simon w mianowicie powiązana z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w mianowicie aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą, wykonaj następujące czynności:**

1. W witrynie Azure portal na **mianowicie** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_samlbase.png)

1. Na **: domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.namely.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta mianowicie](https://www.namely.com/contact/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_general_400.png)

1. Na **mianowicie konfiguracji** , kliknij przycisk **skonfigurować mianowicie** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_configure.png) 

1. W innym oknie przeglądarki Zaloguj się na swoje mianowicie firmy lokacji jako administrator.

1. Na pasku narzędzi u góry kliknij **firmy**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_06.png) 

1. Kliknij kartę **Ustawienia**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_07.png) 

1. Kliknij przycisk **SAML**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_08.png) 

1. Na **ustawienia języka SAML** strony, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Kliknij pozycję **Enable SAML** (Włącz protokół SAML). 

    b. W **adres url logowania jednokrotnego dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego adres URL usługi**, które zostały skopiowane z witryny Azure portal.
    
    c. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go do **certyfikatu dostawcy tożsamości** pola tekstowego.
     
    d. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/namely-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-namely-test-user"></a>Tworzenie mianowicie testowe użytkownika

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w mianowicie.

**Aby utworzyć użytkownika o nazwie Britta Simon to znaczy, wykonaj następujące czynności:**

1. Zaloguj się do Twojej mianowicie firmy lokacji jako administrator.

1. Na pasku narzędzi u góry kliknij **osób**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_10.png) 

1. Kliknij przycisk **katalogu** kartę.
   
    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_11.png) 

1. Kliknij przycisk **Dodawanie nowej osoby**.

    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_12.png)

1. Na **Dodawanie nowej osoby** okno dialogowe, należy wykonać następujące czynności:

    a. W **imię** polu tekstowym wpisz **Britta**.

    b. W **nazwisko** polu tekstowym wpisz **Simon**.

    c. W **E-mail** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    d. Kliknij pozycję **Zapisz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowanie jednokrotne, a mianowicie udzielanie dostępu do.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **mianowicie**.

    ![Konfigurowanie logowania jednokrotnego](./media/namely-tutorial/tutorial_namely_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu mianowicie kafelka w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do mianowicie aplikacji

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/namely-tutorial/tutorial_general_01.png
[2]: ./media/namely-tutorial/tutorial_general_02.png
[3]: ./media/namely-tutorial/tutorial_general_03.png
[4]: ./media/namely-tutorial/tutorial_general_04.png

[100]: ./media/namely-tutorial/tutorial_general_100.png

[200]: ./media/namely-tutorial/tutorial_general_200.png
[201]: ./media/namely-tutorial/tutorial_general_201.png
[202]: ./media/namely-tutorial/tutorial_general_202.png
[203]: ./media/namely-tutorial/tutorial_general_203.png

