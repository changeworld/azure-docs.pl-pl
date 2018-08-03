---
title: 'Samouczek: Integracja usługi Azure Active Directory z Panopto | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Panopto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 60d4b9f5343aac978c1e32aad39f237d565b2176
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421478"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Samouczek: Integracja usługi Azure Active Directory z Panopto

W tym samouczku dowiesz się, jak zintegrować Panopto w usłudze Azure Active Directory (Azure AD).

Integrowanie Panopto z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Panopto
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Panopto (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Panopto, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Panopto logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Panopto z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-panopto-from-the-gallery"></a>Dodawanie Panopto z galerii
Aby skonfigurować integrację Panopto w usłudze Azure AD, należy dodać Panopto z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Panopto z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Panopto**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panopto-tutorial/tutorial_panopto_search.png)

1. W panelu wyników wybierz **Panopto**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panopto-tutorial/tutorial_panopto_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Panopto w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Panopto do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Panopto musi można ustanowić.

W Panopto, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Panopto, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Panopto](#creating-a-panopto-test-user)**  — aby odpowiednikiem Britta Simon w Panopto połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Panopto.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Panopto, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Panopto** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/panopto-tutorial/tutorial_panopto_samlbase.png)

1. Na **Panopto domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/panopto-tutorial/tutorial_panopto_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenant-name>.panopto.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Panopto](mailto:support@panopto.com‎) aby zyskać tę wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/panopto-tutorial/tutorial_panopto_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/panopto-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Panopto** , kliknij przycisk **skonfigurować Panopto** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/panopto-tutorial/tutorial_panopto_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Panopto.

1. Na pasku narzędzi po lewej stronie kliknij pozycję **systemu**, a następnie kliknij przycisk **dostawców tożsamości**.
   
   ![System](./media/panopto-tutorial/ic777670.png "systemu")
1. Kliknij przycisk **Dodaj dostawcę**.
   
   ![Dostawcy tożsamości](./media/panopto-tutorial/ic777671.png "dostawców tożsamości")
   
1. W sekcji dostawcy protokołu SAML wykonaj następujące czynności:
   
    ![Konfiguracja SaaS](./media/panopto-tutorial/ic777672.png "konfiguracji SaaS")
    
    a. Z **typ dostawcy** listy wybierz **SAML20**.    
    
    b. W **nazwa wystąpienia** polu tekstowym wpisz nazwę dla tego wystąpienia.

    c. W **przyjazny opis** polu tekstowym wpisz przyjazny opis.
    
    d. W **Odbijanie adres Url strony** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    e. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal.

    f. Otwórz certyfikat zakodowany base-64, który został pobrany z witryny Azure portal, skopiuj zawartość go do Schowka, a następnie wklej go do **klucza publicznego** pola tekstowego.

1. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panopto-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panopto-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panopto-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/panopto-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-panopto-test-user"></a>Tworzenie użytkownika testowego Panopto

Brak elementu działania umożliwiające skonfigurowanie aprowizacji Panopto użytkowników.  
Gdy przypisany użytkownik próbuje zalogować się do Panopto za pomocą panelu dostępu, Panopto sprawdza, czy użytkownik istnieje.  

Jeśli nie ma użytkownika konta dostępne jeszcze, są tworzone przez Panopto.

>[!NOTE]
>Można użyć jakichkolwiek innych Panopto użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Panopto można uaktywniać ich konta usługi Azure AD.
>
>

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Panopto.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Panopto, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Panopto**.

    ![Konfigurowanie logowania jednokrotnego](./media/panopto-tutorial/tutorial_panopto_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Panopto w panelu dostępu, powinna pojawić się automatycznie strony logowania Panopto aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/panopto-tutorial/tutorial_general_01.png
[2]: ./media/panopto-tutorial/tutorial_general_02.png
[3]: ./media/panopto-tutorial/tutorial_general_03.png
[4]: ./media/panopto-tutorial/tutorial_general_04.png

[100]: ./media/panopto-tutorial/tutorial_general_100.png

[200]: ./media/panopto-tutorial/tutorial_general_200.png
[201]: ./media/panopto-tutorial/tutorial_general_201.png
[202]: ./media/panopto-tutorial/tutorial_general_202.png
[203]: ./media/panopto-tutorial/tutorial_general_203.png

