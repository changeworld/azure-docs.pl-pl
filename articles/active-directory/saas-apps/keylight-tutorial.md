---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą LockPath Keylight | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc5121f6604fae9a28b52db1bfb308d7cdb968d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60263523"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą LockPath Keylight

W tym samouczku dowiesz się, jak zintegrować LockPath Keylight w usłudze Azure Active Directory (Azure AD).

Integrowanie LockPath Keylight z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do LockPath Keylight
- Użytkowników, aby automatycznie uzyskać zalogowanych do LockPath Keylight (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą LockPath Keylight, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- LockPath Keylight logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LockPath Keylight z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Dodawanie LockPath Keylight z galerii
Aby skonfigurować integrację LockPath Keylight w usłudze Azure AD, należy dodać LockPath Keylight z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LockPath Keylight z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **LockPath Keylight**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keylight-tutorial/tutorial_keylight_search.png)

1. W panelu wyników wybierz **LockPath Keylight**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurujesz a test usługi Azure AD logowanie jednokrotne za pomocą LockPath Keylight oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LockPath Keylight do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w LockPath Keylight musi zostać ustanowione.

LockPath Keylight przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą LockPath Keylight, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  — aby odpowiednikiem Britta Simon w LockPath Keylight, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LockPath Keylight.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z LockPath Keylight, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LockPath Keylight** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/tutorial_keylight_samlbase.png)

1. Na **LockPath Keylight domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/tutorial_keylight_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.keylightgrc.com/`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.keylightgrc.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Keylight LockPath](https://www.lockpath.com/contact/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/tutorial_keylight_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji Keylight LockPath** , kliknij przycisk **skonfigurować LockPath Keylight** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/tutorial_keylight_configure.png) 

1. Aby włączyć logowanie Jednokrotne w LockPath Keylight, wykonaj następujące czynności:
   
    a. Zaloguj się do swojego konta LockPath Keylight jako administrator.
    
    b. W menu u góry kliknij **osoby**i wybierz **instalacji Keylight**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/401.png) 

    c. W widoku drzewa po lewej stronie kliknij pozycję **SAML**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/402.png) 

    d. Na **ustawienia języka SAML** okno dialogowe, kliknij przycisk **Edytuj**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/404.png) 

1. Na **edytowanie ustawień protokołu SAML** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/405.png) 
   
    a. Ustaw **uwierzytelnianie SAML** do **Active**.

    b. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartość skopiowaną z witryny Azure portal do **adres URL logowania dostawcy tożsamości** pola tekstowego.

    c. Wklej **adresu URL usługi wylogowania jednokrotnego** wartość skopiowaną z witryny Azure portal do **adres URL wylogowania dostawcy tożsamości** pola tekstowego.

    d. Kliknij przycisk **wybierz plik** wybierz pobrany certyfikat LockPath Keylight, a następnie kliknij przycisk **Otwórz** można przekazać certyfikatu.

    e. Ustaw **lokalizacji identyfikator użytkownika SAML** do **elementu NameIdentifier instrukcji podmiotu**.
    
    f. Podaj **Keylight usługodawcy** przy użyciu następującego wzorca: **https://&lt;CompanyName&gt;. keylightgrc.com**.
    
    g. Ustaw **automatyczne aprowizowanie użytkowników** do **Active**.

    h. Ustaw **automatyczna obsługa typu konta** do **pełnej**.

    i. Ustaw **roli zabezpieczeń automatycznej obsługi**, wybierz opcję **użytkowników standardowych z SAML**.
    
    j. Ustaw **konfiguracji zabezpieczeń automatycznej obsługi**, wybierz opcję **standardowej konfiguracji użytkownika**.
     
    k. W **atrybut E-mail** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. W **atrybut imię** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. W **ostatniego atrybutu nazwy** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keylight-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keylight-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keylight-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/keylight-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Tworzenie użytkownika testowego LockPath Keylight

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w LockPath Keylight. LockPath Keylight obsługę just-in-time, który jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas uzyskiwania dostępu do LockPath Keylight, jeśli użytkownik jeszcze nie istnieje. 

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta Keylight LockPath](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do LockPath Keylight.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon LockPath Keylight, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **LockPath Keylight**.

    ![Konfigurowanie logowania jednokrotnego](./media/keylight-tutorial/tutorial_keylight_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka LockPath Keylight w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji LockPath Keylight. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/keylight-tutorial/tutorial_general_01.png
[2]: ./media/keylight-tutorial/tutorial_general_02.png
[3]: ./media/keylight-tutorial/tutorial_general_03.png
[4]: ./media/keylight-tutorial/tutorial_general_04.png

[100]: ./media/keylight-tutorial/tutorial_general_100.png

[200]: ./media/keylight-tutorial/tutorial_general_200.png
[201]: ./media/keylight-tutorial/tutorial_general_201.png
[202]: ./media/keylight-tutorial/tutorial_general_202.png
[203]: ./media/keylight-tutorial/tutorial_general_203.png

