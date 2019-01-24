---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Online Ekspresyjne | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Ekspresyjne Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4fd6b29b-1b46-4fd1-9f5e-16b1c9d892cd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 9d0424c5a0cb8a8d2b6e5b9f028484613949c3da
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827894"
---
# <a name="tutorial-azure-active-directory-integration-with-bgs-online"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Online Ekspresyjne

W tym samouczku dowiesz się, jak zintegrować Ekspresyjne Online z usługą Azure Active Directory (Azure AD).

Integrowanie Ekspresyjne Online z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Ekspresyjne Online
- Użytkowników, aby automatycznie uzyskać zalogowanych do Ekspresyjne Online (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Online Ekspresyjne, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Ekspresyjne Online logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Ekspresyjne Online za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-bgs-online-from-the-gallery"></a>Dodawanie Ekspresyjne Online za pomocą galerii
Aby skonfigurować integrację Ekspresyjne online w usłudze Azure AD, należy dodać Ekspresyjne Online z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Ekspresyjne Online z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Ekspresyjne Online**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bgsonline-tutorial/tutorial_bgsonline_search.png)

1. W panelu wyników wybierz **Ekspresyjne Online**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bgsonline-tutorial/tutorial_bgsonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne z usługą Online Ekspresyjne oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Ekspresyjne w trybie Online dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w dokumentacji Online Ekspresyjne musi można ustanowić.

W trybie Online Ekspresyjne przypisze się wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą Ekspresyjne Online, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Ekspresyjne Online](#creating-a-bgs-online-test-user)**  — aby odpowiednikiem Britta Simon w Ekspresyjne Online, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Ekspresyjne Online.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z usługą Ekspresyjne Online, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Ekspresyjne Online** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/bgsonline-tutorial/tutorial_bgsonline_samlbase.png)

1. Na **adresy URL i domen w trybie Online Ekspresyjne** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/bgsonline-tutorial/tutorial_bgsonline_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    Użyj tego wzorca, w środowisku produkcyjnym `https://<company name>.millwardbrown.report` 

    Użyj tego wzorca, dla środowiska testowego `https://millwardbrown.marketingtracker.nl/mt5/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:
    
    Użyj tego wzorca, w środowisku produkcyjnym `https://<company name>.millwardbrown.report/sso/saml/AssertionConsumerService.aspx` 
      
    Użyj tego wzorca, dla środowiska testowego `https://millwardbrown.marketingtracker.nl/mt5/sso/saml/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej Online Ekspresyjne](mailTo:bgsdashboardteam@millwardbrown.com) do uzyskania tych wartości.
 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/bgsonline-tutorial/tutorial_bgsonline_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/bgsonline-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Online Ekspresyjne** , kliknij przycisk **skonfigurować Online Ekspresyjne** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/bgsonline-tutorial/tutorial_bgsonline_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Ekspresyjne Online** stronie, musisz wysłać pobrany **XML metadanych** i **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [Ekspresyjne Online zespołu pomocy technicznej](mailto:bgsdashboardteam@millwardbrown.com). 


> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bgsonline-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bgsonline-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bgsonline-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bgsonline-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-bgs-online-test-user"></a>Tworzenie użytkownika testowego Ekspresyjne Online

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Ekspresyjne w trybie Online. Praca z [zespołem pomocy technicznej Online Ekspresyjne](mailto:bgsdashboardteam@millwardbrown.com) Aby dodać użytkowników na platformie Ekspresyjne w trybie Online.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Ekspresyjne Online.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Ekspresyjne Online, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Ekspresyjne Online**.

    ![Konfigurowanie logowania jednokrotnego](./media/bgsonline-tutorial/tutorial_bgsonline_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji możesz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka Ekspresyjne Online, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Ekspresyjne Online aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bgsonline-tutorial/tutorial_general_01.png
[2]: ./media/bgsonline-tutorial/tutorial_general_02.png
[3]: ./media/bgsonline-tutorial/tutorial_general_03.png
[4]: ./media/bgsonline-tutorial/tutorial_general_04.png

[100]: ./media/bgsonline-tutorial/tutorial_general_100.png

[200]: ./media/bgsonline-tutorial/tutorial_general_200.png
[201]: ./media/bgsonline-tutorial/tutorial_general_201.png
[202]: ./media/bgsonline-tutorial/tutorial_general_202.png
[203]: ./media/bgsonline-tutorial/tutorial_general_203.png

