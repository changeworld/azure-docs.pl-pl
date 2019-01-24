---
title: 'Samouczek: Integracja usługi Azure Active Directory z plikami płaski | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i płaski plików.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: aaf6c6ba17e41c4d32aafa98dbd2c1dc2532e197
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808310"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Samouczek: Integracja usługi Azure Active Directory z plikami płaski

W tym samouczku dowiesz się, jak zintegrować płaski pliki z usługi Azure Active Directory (Azure AD).

Integrowanie płaski pliki z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do plików płaski
- Użytkowników, aby automatycznie uzyskać zalogowanych do płaski plików (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z plikami płaski, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Pliki płaski logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Trwa dodawanie plików płaski z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-flatter-files-from-the-gallery"></a>Trwa dodawanie plików płaski z galerii
Aby skonfigurować integrację płaski plików w usłudze Azure AD, musisz dodać pliki płaski z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pliki płaski z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **płaski pliki**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/flatter-files-tutorial/tutorial_flatterfiles_search.png)

1. W panelu wyników wybierz **płaski pliki**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą płaski plików, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w plikach płaski do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w plikach płaski musi zostać ustanowione.

Płaski plików, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą płaski plików, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego płaski pliki](#creating-a-flatter-files-test-user)**  — aby odpowiednikiem Britta Simon w płaski plików, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji płaski plików.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z plikami płaski, wykonaj następujące czynności:**

1. W witrynie Azure portal na **płaski pliki** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

1. Na **płaski domena plików i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_general_400.png)

1. Na **płaski pliki konfiguracji** , kliknij przycisk **Konfigurowanie plików płaski** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

1. Logowanie jednokrotne do aplikacji płaski plików jako administrator.

1. Kliknij przycisk **pulpit NAWIGACYJNY**. 
   
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

1. Kliknij przycisk **ustawienia**, a następnie wykonaj następujące czynności na **firmy** karty: 
   
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Wybierz **używać uwierzytelniania SAML 2.0 na potrzeby uwierzytelniania**.
    
    b. Kliknij pozycję **Skonfiguruj język SAML**.

1. Na **plik konfiguracji SAML** okno dialogowe, należy wykonać następujące czynności: 
   
    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. W **domeny** polu tekstowym wpisz domenę zarejestrowane.
   
    >[!NOTE]
    >Jeśli nie posiadasz zarejestrowanej domeny jeszcze, skontaktuj się z zespołem za pośrednictwem pomocy technicznej płaski plików [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. W **adres URL dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** który skopiowano tworzą witryny Azure portal.
   
    c.  Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości** pola tekstowego.

    d. Kliknij przycisk **Update** (Aktualizuj).

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/flatter-files-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/flatter-files-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/flatter-files-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/flatter-files-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-flatter-files-test-user"></a>Tworzenie użytkownika testowego płaski plików

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w plikach płaski.

**Aby utworzyć użytkownika o nazwie Britta Simon w plikach płaski, wykonaj następujące czynności:**

1. Zaloguj się na swoje **płaski pliki** witryny firmy jako administrator.

1. W okienku nawigacji po lewej stronie kliknij pozycję **ustawienia**, a następnie kliknij przycisk **użytkowników** kartę.
   
    ![Utwórz użytkownika płaski plików](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

1. Kliknij pozycję **Add User** (Dodaj użytkownika). 

1. Na **Dodaj użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
    ![Utwórz użytkownika płaski plików](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. W **imię** polu tekstowym wpisz **Britta**.
   
    b. W **nazwisko** polu tekstowym wpisz **Simon**. 
   
    c. W **adres E-mail** pole tekstowe, wpisz adres e-mail Britty w witrynie Azure portal.
   
    d. Kliknij przycisk **Prześlij**.   


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do plików płaski.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon płaski plików, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **płaski pliki**.

    ![Konfigurowanie logowania jednokrotnego](./media/flatter-files-tutorial/tutorial_flatterfiles_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka płaski pliki w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do płaski pliki aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/flatter-files-tutorial/tutorial_general_203.png

