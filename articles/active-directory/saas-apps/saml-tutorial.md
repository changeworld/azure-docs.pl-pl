---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu tokenu języka SAML 1.1 włączona aplikacji LOB | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacji LOB z obsługą tokenu języka SAML 1.1.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: edabc09f820093d088ec0b8ed1222fb26c800bee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426432"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu tokenu języka SAML 1.1 włączona aplikacji LOB

W tym samouczku dowiesz się, jak zintegrować tokenu języka SAML 1.1 aplikacji z włączoną obsługą LOB za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie tokenu języka SAML 1.1 włączone aplikacji LOB za pomocą usługi Azure AD oferuje następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do tokenu języka SAML 1.1 włączone aplikacji LOB.
- Umożliwia użytkownikom automatycznie uzyskać zalogowanych do tokenu języka SAML 1.1 aplikacji z obsługą LOB (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować Azure aplikacji LOB z włączoną obsługą integracji z usługą AD za pomocą tokenu języka SAML 1.1, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Tokenu języka SAML 1.1 włączone aplikacji LOB pojedynczej logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie tokenu języka SAML 1.1 włączone aplikacji LOB za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Dodawanie tokenu języka SAML 1.1 włączone aplikacji LOB za pomocą galerii
Aby skonfigurować integrację z tokenu języka SAML 1.1 aplikacji z włączoną obsługą LOB w usłudze Azure AD, należy dodać z obsługą tokenu języka SAML 1.1 aplikacji LOB za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LOB aplikacji z galerii z obsługą tokenu języka SAML 1.1, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **aplikacji LOB z obsługą tokenu języka SAML 1.1**, wybierz opcję **aplikacji LOB z obsługą tokenu języka SAML 1.1** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Tokenu języka SAML 1.1 aplikacji z obsługą LOB na liście wyników](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu tokenu języka SAML 1.1 aplikacji LOB włączony oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w tokenu języka SAML 1.1 włączona aplikacja LOB to dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w tokenu języka SAML 1.1 włączone aplikacji LOB powinien być określony.

Do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu tokenu języka SAML 1.1 włączone aplikacji LOB, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie tokenu języka SAML 1.1 włączone użytkownika testowego aplikacji LOB](#create-a-saml-11-token-enabled-lob-app-test-user)**  — aby aplikacji LOB, połączonego z usługi Azure AD reprezentacja użytkownika z włączoną obsługą odpowiednikiem Britta Simon w tokenu języka SAML 1.1.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w tokenu usługi języka SAML 1.1 aplikacji LOB aplikacji z obsługą.

**Do konfigurowania usługi Azure AD logowania jednokrotnego przy użyciu tokenu języka SAML 1.1 aplikacji z obsługą LOB, wykonaj następujące czynności:**

1. W witrynie Azure portal na **aplikacji LOB z obsługą tokenu języka SAML 1.1** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/saml-tutorial/tutorial_saml_samlbase.png)

1. Na **włączone tokenu języka SAML 1.1, domena aplikacji LOB i adresy URL** sekcji, wykonaj następujące czynności:

    ![Tokenu języka SAML 1.1 włączone domena aplikacji LOB i adresy URL pojedynczego logowania jednokrotnego informacji](./media/saml-tutorial/tutorial_saml_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://your-app-url`

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://your-app-url`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Zastąp te wartości z określonego adresu URL aplikacji.  

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/saml-tutorial/tutorial_saml_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/saml-tutorial/tutorial_general_400.png)
    
1. Na **LOB konfiguracji aplikacji z włączonym tokenu języka SAML 1.1** , kliknij przycisk **Konfigurowanie tokenu języka SAML 1.1 aplikacji z włączoną obsługą LOB** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![LOB konfiguracji aplikacji z włączonym tokenu języka SAML 1.1](./media/saml-tutorial/tutorial_saml_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **aplikacji LOB z obsługą tokenu języka SAML 1.1** stronie, musisz wysłać pobrany **certyfikat (Base64), adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** do zespół pomocy technicznej dla aplikacji. Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/saml-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/saml-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/saml-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/saml-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Tworzenie tokenu języka SAML 1.1 włączone użytkownika testowego aplikacji LOB

W tej sekcji opisano tworzenie użytkownika o nazwie Britta Simon aplikacji LOB z obsługą tokenu języka SAML 1.1. Praca z aplikacją obsługuje zespołu, aby utworzyć użytkownika na stronie aplikacji. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon za pomocą platformy Azure zostanie włączone logowanie jednokrotne za udzielanie dostępu do tokenu języka SAML 1.1 włączone aplikacji LOB.

![Przypisanie roli użytkownika][200] 

**Do przypisywania Britta Simon do tokenu języka SAML 1.1 włączone aplikacji LOB, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **aplikacji LOB z obsługą tokenu języka SAML 1.1**.

    ![Tokenu języka SAML 1.1 włączone link do aplikacji LOB na liście aplikacji](./media/saml-tutorial/tutorial_saml_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu tokenu języka SAML 1.1 włączone kafelka aplikacji LOB w panelu dostępu, należy pobrać automatycznie zalogowanych do aplikacji LOB aplikacji z obsługą tokenu usługi języka SAML 1.1.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
