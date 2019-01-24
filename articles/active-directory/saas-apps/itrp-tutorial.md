---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą ITRP | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 1ba77414906f90d14809147e532379ae461008dc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817575"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą ITRP

W tym samouczku dowiesz się, jak zintegrować ITRP w usłudze Azure Active Directory (Azure AD).

Integrowanie ITRP z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do ITRP
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do ITRP (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ITRP, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- ITRP logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ITRP z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-itrp-from-the-gallery"></a>Dodawanie ITRP z galerii
Aby skonfigurować integrację ITRP w usłudze Azure AD, należy dodać ITRP z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ITRP z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **ITRP**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/itrp-tutorial/tutorial_itrp_search.png)

1. W panelu wyników wybierz **ITRP**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą ITRP w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ITRP do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ITRP musi można ustanowić.

W ITRP, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą ITRP, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie ITRP testowe użytkownika](#creating-an-itrp-test-user)**  — aby odpowiednikiem Britta Simon w ITRP połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ITRP.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z ITRP, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ITRP** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/itrp-tutorial/tutorial_itrp_samlbase.png)

1. Na **ITRP domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/itrp-tutorial/tutorial_itrp_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant-name>.itrp.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant-name>.itrp.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta ITRP](https://www.itrp.com/support) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/itrp-tutorial/tutorial_itrp_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/itrp-tutorial/tutorial_general_400.png)

1. Na **konfiguracji ITRP** , kliknij przycisk **skonfigurować ITRP** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL i adresu URL wylogowania** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/itrp-tutorial/tutorial_itrp_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy ITRP.

1. Na pasku narzędzi u góry kliknij **ustawienia**.
   
    ![ITRP](./media/itrp-tutorial/ic775570.png "ITRP")

1. W okienku nawigacji po lewej stronie wybierz **logowania jednokrotnego**.
   
    ![Logowanie jednokrotne](./media/itrp-tutorial/ic775571.png "Logowanie jednokrotne")

1. W sekcji konfiguracji logowania jednokrotnego wykonaj następujące czynności:
   
    ![Logowanie jednokrotne](./media/itrp-tutorial/ic775572.png "Logowanie jednokrotne")
    
    ![Logowanie jednokrotne](./media/itrp-tutorial/ic775573.png "Logowanie jednokrotne")   

    a. Kliknij przycisk **Włącz**.

    b. W **zdalnego adresu URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    c. W **adres URL logowania jednokrotnego SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego adres URL usługi**, które zostały skopiowane z witryny Azure portal.

    d.In **odcisk palca certyfikatu** pola tekstowego, Wklej **odcisk palca** wartość certyfikatu, który skopiowano z witryny Azure portal. 
      
1. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/itrp-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/itrp-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/itrp-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/itrp-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-itrp-test-user"></a>Tworzenie użytkownika testowego ITRP

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do ITRP, ich musi być obsługiwana w celu ITRP.  

W przypadku ITRP Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **ITRP** dzierżawy.

1. Na pasku narzędzi u góry kliknij **rekordów**.
   
    ![Administrator](./media/itrp-tutorial/ic775575.png "Administrator")

1. Wybierz z menu podręcznego **osób**.
   
    ![Osoby](./media/itrp-tutorial/ic775587.png "Osoby")

1. Kliknij przycisk **Dodawanie nowej osoby** ("+").
   
    ![Administrator](./media/itrp-tutorial/ic775576.png "Administrator")

1. W oknie dialogowym Dodawanie nowej osoby wykonaj następujące czynności:
   
    ![Użytkownik](./media/itrp-tutorial/ic775577.png "Użytkownik") 
      
    a. Typ **nazwa**, **E-mail** poprawnego konta usługi AAD do aprowizowania.

    b. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Można użyć jakichkolwiek innych ITRP użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez ITRP do aprowizacji kont użytkowników usługi AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ITRP.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon ITRP, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **ITRP**.

    ![Konfigurowanie logowania jednokrotnego](./media/itrp-tutorial/tutorial_itrp_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ITRP w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ITRP.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/itrp-tutorial/tutorial_general_01.png
[2]: ./media/itrp-tutorial/tutorial_general_02.png
[3]: ./media/itrp-tutorial/tutorial_general_03.png
[4]: ./media/itrp-tutorial/tutorial_general_04.png

[100]: ./media/itrp-tutorial/tutorial_general_100.png

[200]: ./media/itrp-tutorial/tutorial_general_200.png
[201]: ./media/itrp-tutorial/tutorial_general_201.png
[202]: ./media/itrp-tutorial/tutorial_general_202.png
[203]: ./media/itrp-tutorial/tutorial_general_203.png

