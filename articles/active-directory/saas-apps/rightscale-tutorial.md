---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Rightscale | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 416a98c5f9c5a2ec813206ea9ea7f311b23e86cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61080748"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Rightscale

W tym samouczku dowiesz się, jak zintegrować Rightscale w usłudze Azure Active Directory (Azure AD).

Integrowanie Rightscale z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Rightscale
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Rightscale (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Rightscale, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Rightscale logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Rightscale z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-rightscale-from-the-gallery"></a>Dodawanie Rightscale z galerii
Aby skonfigurować integrację Rightscale w usłudze Azure AD, należy dodać Rightscale z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Rightscale z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Rightscale**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rightscale-tutorial/tutorial_rightscale_search.png)

1. W panelu wyników wybierz **Rightscale**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Rightscale w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Rightscale do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Rightscale musi można ustanowić.

W Rightscale, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Rightscale, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Rightscale](#creating-a-rightscale-test-user)**  — aby odpowiednikiem Britta Simon w Rightscale połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Rightscale.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Rightscale, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Rightscale** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

1. Na **Rightscale domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tryb inicjowane przez dostawcę tożsamości** nie trzeba wykonywać żadnych czynności, ponieważ aplikacja jest już wstępnie zintegrowane z usługą Azure.

    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_url.png)

1. Na **Rightscale domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjowano tryb**, wykonaj następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL**.

    b. W **na adres URL logowania** pole tekstowe, wpisz adres URL: `https://login.rightscale.com/`

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Rightscale** , kliknij przycisk **skonfigurować Rightscale** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, musisz logowanie jednokrotne do swojej dzierżawy RightScale jako administrator.

    a. W menu u góry kliknij **ustawienia** kartę, a następnie wybierz pozycję **logowania jednokrotnego**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Kliknij przycisk "**nowe**" przycisk, aby dodać **Your dostawcy tożsamości SAML**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. W polu tekstowym z **nazwę wyświetlaną**, wprowadź nazwę swojej firmy.
   
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Wybierz **logowanie Jednokrotne zainicjowane przez Zezwalaj RightScale, używając wskazówki odnajdywania** i danych wejściowych użytkownika **nazwy domeny** w poniżej pola tekstowego.
   
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal do **punktu końcowego logowania jednokrotnego SAML** w RightScale.
   
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal do **SAML EntityID** w RightScale.
   
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. Kliknij przycisk **przeglądarki** przycisk, aby przekazać certyfikat, który został pobrany z witryny Azure portal.
   
    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. Kliknij pozycję **Zapisz**.

   > [!TIP]
   > Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
   > 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rightscale-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rightscale-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rightscale-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-rightscale-test-user"></a>Tworzenie użytkownika testowego Rightscale

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w RightScale. Praca z [zespołem pomocy technicznej klienta Rightscale](mailto:support@rightscale.com) Aby dodać użytkowników na platformie RightScale.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Rightscale.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Rightscale, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Rightscale**.

    ![Konfigurowanie logowania jednokrotnego](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.  

Po kliknięciu kafelka RightScale w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji RightScale.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

