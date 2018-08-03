---
title: 'Samouczek: Integracja usługi Azure Active Directory z SumoLogic | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: dbebf2605fb214a167a276ec8dc344ff450ae5c0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420074"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Samouczek: Integracja usługi Azure Active Directory z SumoLogic

W tym samouczku dowiesz się, jak zintegrować SumoLogic w usłudze Azure Active Directory (Azure AD).

Integrowanie SumoLogic z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do SumoLogic
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do SumoLogic (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą SumoLogic, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SumoLogic logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SumoLogic z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sumologic-from-the-gallery"></a>Dodawanie SumoLogic z galerii
Aby skonfigurować integrację SumoLogic w usłudze Azure AD, należy dodać SumoLogic z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SumoLogic z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **SumoLogic**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sumologic-tutorial/tutorial_sumologic_search.png)

1. W panelu wyników wybierz **SumoLogic**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą SumoLogic w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w SumoLogic do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SumoLogic musi można ustanowić.

W SumoLogic, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SumoLogic, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego SumoLogic](#creating-a-sumologic-test-user)**  — aby odpowiednikiem Britta Simon w SumoLogic połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SumoLogic.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z SumoLogic, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SumoLogic** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/sumologic-tutorial/tutorial_sumologic_samlbase.png)

1. Na **SumoLogic domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sumologic-tutorial/tutorial_sumologic_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantname>.SumoLogic.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta SumoLogic](https://www.sumologic.com/contact-us/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/sumologic-tutorial/tutorial_sumologic_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/sumologic-tutorial/tutorial_general_400.png)

1. Na **konfiguracji SumoLogic** , kliknij przycisk **skonfigurować SumoLogic** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/sumologic-tutorial/tutorial_sumologic_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy SumoLogic.

1. Przejdź do **zarządzanie \> zabezpieczeń**.
   
    ![Zarządzanie](./media/sumologic-tutorial/ic778556.png "Zarządzanie")

1. Kliknij przycisk **SAML**.
   
    ![Ustawienia zabezpieczeń globalnych](./media/sumologic-tutorial/ic778557.png "ustawienia zabezpieczeń globalnych")

1. Z **wybierz konfigurację, lub utworzyć nowy** , wybierz na liście **usługi Azure AD**, a następnie kliknij przycisk **Konfiguruj**.
   
    ![Konfigurowanie uwierzytelniania SAML 2.0](./media/sumologic-tutorial/ic778558.png "skonfigurować SAML 2.0")

1. Na **skonfigurować SAML 2.0** okno dialogowe, należy wykonać następujące czynności:
   
    ![Konfigurowanie uwierzytelniania SAML 2.0](./media/sumologic-tutorial/ic778559.png "skonfigurować SAML 2.0")
   
    a. W **Nazwa konfiguracji** polu tekstowym wpisz **usługi Azure AD**. 

    b. Wybierz **tryb debugowania**.

    c. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal. 

    d. W **adresu URL żądania uwierzytelniania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    e. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej cały certyfikat do **certyfikat X.509** pola tekstowego.

    f. Jako **atrybut E-mail**, wybierz opcję **podmiotu SAML użyj**.  

    g. Wybierz **SP zainicjowano konfigurację logowania**.

    h. W **ścieżkę logowania** polu tekstowym wpisz **Azure** i kliknij przycisk **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sumologic-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sumologic-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sumologic-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sumologic-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-sumologic-test-user"></a>Tworzenie użytkownika testowego SumoLogic

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do SumoLogic, musi być obsługiwana na SumoLogic.  

* W przypadku SumoLogic Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **SumoLogic** dzierżawy.

1. Przejdź do **zarządzanie \> użytkowników**.
   
    ![Użytkownicy](./media/sumologic-tutorial/ic778561.png "użytkowników")

1. Kliknij pozycję **Add** (Dodaj).
   
    ![Użytkownicy](./media/sumologic-tutorial/ic778562.png "użytkowników")

1. Na **nowego użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
    ![Nowy użytkownik](./media/sumologic-tutorial/ic778563.png "nowego użytkownika") 
 
    a. Wpisz odpowiednie informacje konta usługi Azure AD, do aprowizowania w **imię**, **nazwisko**, i **E-mail** pól tekstowych.
  
    b. Wybierz rolę.
  
    c. Jako **stan**, wybierz opcję **Active**.
  
    d. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Można użyć jakichkolwiek innych SumoLogic użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez SumoLogic do aprowizacji kont użytkowników usługi AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do SumoLogic.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon SumoLogic, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **SumoLogic**.

    ![Konfigurowanie logowania jednokrotnego](./media/sumologic-tutorial/tutorial_sumologic_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SumoLogic w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SumoLogic.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sumologic-tutorial/tutorial_general_01.png
[2]: ./media/sumologic-tutorial/tutorial_general_02.png
[3]: ./media/sumologic-tutorial/tutorial_general_03.png
[4]: ./media/sumologic-tutorial/tutorial_general_04.png

[100]: ./media/sumologic-tutorial/tutorial_general_100.png

[200]: ./media/sumologic-tutorial/tutorial_general_200.png
[201]: ./media/sumologic-tutorial/tutorial_general_201.png
[202]: ./media/sumologic-tutorial/tutorial_general_202.png
[203]: ./media/sumologic-tutorial/tutorial_general_203.png

