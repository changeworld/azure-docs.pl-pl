---
title: 'Samouczek: Integracja usługi Azure Active Directory z EverBridge | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 283379131b02f4ea115052f051ef0114efab1997
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423195"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Samouczek: Integracja usługi Azure Active Directory z EverBridge

W tym samouczku dowiesz się, jak zintegrować EverBridge w usłudze Azure Active Directory (Azure AD).

Integrowanie EverBridge z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do EverBridge
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do EverBridge (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą EverBridge, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Logowania jednokrotnego EverBridge włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie EverBridge z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-everbridge-from-the-gallery"></a>Dodawanie EverBridge z galerii
Aby skonfigurować integrację EverBridge w usłudze Azure AD, należy dodać EverBridge z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać EverBridge z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **EverBridge**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/everbridge-tutorial/tutorial_everbridge_search.png)

1. W panelu wyników wybierz **EverBridge**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą EverBridge w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w EverBridge do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w EverBridge musi można ustanowić.

W EverBridge, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą EverBridge, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego EverBridge](#creating-an-everbridge-test-user)**  — aby odpowiednikiem Britta Simon w EverBridge połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji EverBridge.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z EverBridge, wykonaj następujące czynności:**

1. W witrynie Azure portal na **EverBridge** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_samlbase.png)

1. Na **EverBridge domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://sso.everbridge.net/<companyname>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://manager.everbridge.net/saml/SSO/<companyname>/alias/defaultAlias`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com) do uzyskania tych wartości.
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_general_400.png)

1. Na **konfiguracji EverBridge** , kliknij przycisk **skonfigurować EverBridge** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_configure.png) 

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, musisz logowanie jednokrotne do swojej dzierżawy Everbridge jako administrator.

1. W menu u góry kliknij **ustawienia** kartę, a następnie wybierz pozycję **logowania jednokrotnego** w obszarze **zabezpieczeń**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. W **nazwa** polu tekstowym wpisz nazwę identyfikatora dostawcy (na przykład: Nazwa firmy).
   
    b. W **Nazwa interfejsu API** polu tekstowym wpisz nazwę interfejsu API.
   
    c. Kliknij przycisk **wybierz plik** przycisk, aby przekazać plik metadanych, który został pobrany z witryny Azure portal.
   
    d. W tym miejscu tożsamości SAML, wybierz **tożsamość jest w elemencie NameIdentifier instrukcji podmiotu**.
   
    e. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość adres URL logowania jednokrotnego SAML z usługi Azure AD.
   
    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_003.png)
   
    f. W dostawcy inicjowane żądania powiązania usługi, wybierz **przekierowania HTTP**.

    g. Kliknij pozycję **Zapisz**

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/everbridge-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/everbridge-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/everbridge-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/everbridge-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-everbridge-test-user"></a>Tworzenie użytkownika testowego EverBridge

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Everbridge. Praca z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com) Aby dodać użytkowników na platformie Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do EverBridge.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon EverBridge, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **EverBridge**.

    ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Everbridge w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Everbridge.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/everbridge-tutorial/tutorial_general_01.png
[2]: ./media/everbridge-tutorial/tutorial_general_02.png
[3]: ./media/everbridge-tutorial/tutorial_general_03.png
[4]: ./media/everbridge-tutorial/tutorial_general_04.png

[100]: ./media/everbridge-tutorial/tutorial_general_100.png

[200]: ./media/everbridge-tutorial/tutorial_general_200.png
[201]: ./media/everbridge-tutorial/tutorial_general_201.png
[202]: ./media/everbridge-tutorial/tutorial_general_202.png
[203]: ./media/everbridge-tutorial/tutorial_general_203.png

