---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacji DocuSign | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 5d8aef1edf4d7a02686db48d3e788e4f9493c398
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448489"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacji DocuSign

W tym samouczku dowiesz się, jak zintegrować DocuSign w usłudze Azure Active Directory (Azure AD).

Integrowanie aplikacji DocuSign z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji DocuSign
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do DocuSign (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD aplikacji docusign, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- DocuSign logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie aplikacji DocuSign z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie aplikacji DocuSign z galerii
Aby skonfigurować integrację DocuSign w usłudze Azure AD, należy dodać DocuSign z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać DocuSign w galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **DocuSign**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/docusign-tutorial/tutorial_docusign_search.png)

1. W panelu wyników wybierz **DocuSign**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowania jednokrotnego aplikacji docusign w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze DocuSign do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze DocuSign musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w usłudze DocuSign.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego aplikacji docusign, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego DocuSign](#creating-a-docusign-test-user)**  — aby odpowiednikiem Britta Simon w aplikacji DocuSign, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji DocuSign.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego aplikacji docusign, wykonaj następujące czynności:**

1. W witrynie Azure portal na **DocuSign** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/docusign-tutorial/tutorial_docusign_samlbase.png)

1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base 64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

1. Na **konfiguracji DocuSign** części witryny Azure portal kliknij pozycję **skonfigurować DocuSign** otworzyć Konfigurowanie logowania jednokrotnego okno. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**
    
    ![Konfigurowanie logowania jednokrotnego](./media/docusign-tutorial/tutorial_docusign_configure.png)

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego DocuSign** jako administrator.

1. W menu nawigacji po lewej stronie kliknij pozycję **domen**.
   
    ![Konfigurowanie logowania jednokrotnego][51]

1. W okienku po prawej stronie kliknij **oświadczenia domeny**.
   
    ![Konfigurowanie logowania jednokrotnego][52]

1. Na **oświadczenia domeny** okna dialogowego w **nazwy domeny** polu tekstowym wpisz domenę firmy, a następnie kliknij przycisk **oświadczenia**. Upewnij się, że zweryfikować domenę, a stan jest aktywny.
   
    ![Konfigurowanie logowania jednokrotnego][53]

1. W menu po lewej stronie kliknij pozycję **dostawców tożsamości**  
   
    ![Konfigurowanie logowania jednokrotnego][54]
1. W okienku po prawej stronie kliknij **Dodawanie dostawcy tożsamości**. 
   
    ![Konfigurowanie logowania jednokrotnego][55]

1. Na **ustawień dostawcy tożsamości** strony, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego][56]

    a. W **nazwa** pole tekstowe, wpisz unikatową nazwę dla danej konfiguracji. Nie należy używać miejsc do magazynowania.

    b. Wklej **identyfikator jednostki SAML** do **wystawca dostawcy tożsamości** pola tekstowego.

    c. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do **adres URL logowania dostawcy tożsamości** pola tekstowego.

    d. Wklej **adres URL wylogowania** do **adres URL wylogowania dostawcy tożsamości** pola tekstowego.

    e. Wybierz **podpisywania żądania uwierzytelniania**.

    f. Jako **żądania wysyłania AuthN**, wybierz opcję **WPIS**.

    g. Jako **wysyłania żądania wylogowania**, wybierz opcję **UZYSKAĆ**.

1. W **mapowanie atrybutu niestandardowego** sekcji, wybierz pole, którego chcesz mapować przy użyciu usługi Azure AD oświadczenia. W tym przykładzie **emailaddress** oświadczeń jest zamapowana z wartością **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Jest domyślna nazwa oświadczeń z usługi Azure AD dla oświadczenie adresu e-mail. 
   
    > [!NOTE]
    > To zrobić w odpowiednim **identyfikator użytkownika** do mapowania użytkowników z usługi Azure AD do mapowania użytkowników DocuSign. Zaznacz odpowiednie pola, a następnie wprowadź odpowiednią wartość na podstawie własnych ustawień organizacji.
          
    ![Konfigurowanie logowania jednokrotnego][57]

1. W **certyfikatu dostawcy tożsamości** kliknij **Dodaj certyfikat**, a następnie przekaż certyfikat został pobrany z portalu usługi Azure AD.   
   
    ![Konfigurowanie logowania jednokrotnego][58]

1. Kliknij pozycję **Zapisz**.

1. W **dostawców tożsamości** kliknij **akcje**, a następnie kliknij przycisk **punktów końcowych**.   
   
    ![Konfigurowanie logowania jednokrotnego][59]
 
1. W **Wyświetl SAML 2.0 punkty końcowe** sekcji na **portalu administracyjnego DocuSign**, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego][60]
   
    a. Kopiuj **adres URL wystawcy dostawcy usługi**i Wklej do **identyfikator** tekstowe na **DocuSign domena i adresy URL** części witryny Azure portal, zgodnie ze wzorcem: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Kopiowania **adres URL logowania dostawcy usługi**, a następnie wklej w **na adres URL logowania** tekstowe na **DocuSign domena i adresy URL** części witryny Azure portal, zgodnie ze wzorcem: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/` .

    ![Konfigurowanie logowania jednokrotnego](./media/docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Kliknij przycisk **Zamknij**
    
1. W witrynie Azure portal kliknij pozycję **Zapisz**.
    
    ![Konfigurowanie logowania jednokrotnego](./media/docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/docusign-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/docusign-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/docusign-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/docusign-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-docusign-test-user"></a>Tworzenie użytkownika testowego DocuSign

Aplikacja obsługuje **Just in aprowizacji użytkowników w czasie** i po uwierzytelniania użytkowników są tworzone automatycznie w aplikacji.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej DocuSign.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon DocuSign, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **DocuSign**.

    ![Konfigurowanie logowania jednokrotnego](./media/docusign-tutorial/tutorial_docusign_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka DocuSign w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji DocuSign.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/docusign-tutorial/tutorial_general_01.png
[2]: ./media/docusign-tutorial/tutorial_general_02.png
[3]: ./media/docusign-tutorial/tutorial_general_03.png
[4]: ./media/docusign-tutorial/tutorial_general_04.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/docusign-tutorial/tutorial_general_100.png

[200]: ./media/docusign-tutorial/tutorial_general_200.png
[201]: ./media/docusign-tutorial/tutorial_general_201.png
[202]: ./media/docusign-tutorial/tutorial_general_202.png
[203]: ./media/docusign-tutorial/tutorial_general_203.png

