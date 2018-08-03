---
title: 'Samouczek: Integracja usługi Azure Active Directory z Promapp | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: 6bcd1add3985112fe60aab22f1799e40ad8889b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421866"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Samouczek: Integracja usługi Azure Active Directory z Promapp

W tym samouczku dowiesz się, jak zintegrować Promapp w usłudze Azure Active Directory (Azure AD).

Integrowanie Promapp z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Promapp
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Promapp (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Promapp, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Promapp logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Promapp z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-promapp-from-the-gallery"></a>Dodawanie Promapp z galerii
Aby skonfigurować integrację Promapp w usłudze Azure AD, należy dodać Promapp z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Promapp z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Promapp**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/promapp-tutorial/tutorial_promapp_search.png)

1. W panelu wyników wybierz **Promapp**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Promapp w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Promapp do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Promapp musi można ustanowić.

W Promapp, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Promapp, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Promapp](#creating-a-promapp-test-user)**  — aby odpowiednikiem Britta Simon w Promapp połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Promapp.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Promapp, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Promapp** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/promapp-tutorial/tutorial_promapp_samlbase.png)

1. Na **Promapp domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/promapp-tutorial/tutorial_promapp_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    | |
    |--|
    | `https://go.promapp.com/TENANTNAME/`|
    | `https://au.promapp.com/TENANTNAME/`|
    | `https://us.promapp.com/TENANTNAME/`|
    | `https://eu.promapp.com/TENANTNAME/`|
    | `https://ca.promapp.com/TENANTNAME/`|
    
    > [!NOTE] 
    > Obecnie integracji usługi Azure AD za pomocą Promapp tylko został skonfigurowany do uwierzytelniania zainicjowane przez usługi, np. przechodząc do adresu URL Promapp inicjuje proces uwierzytelniania. Jednak adres URL odpowiedzi jest polem wymaganym.
    
    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/promapp-tutorial/tutorial_promapp_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Promapp](https://www.promapp.com/about-us/contact-us/) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/promapp-tutorial/tutorial_promapp_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/promapp-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Promapp** , kliknij przycisk **skonfigurować Promapp** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/promapp-tutorial/tutorial_promapp_configure.png) 

1. Zaloguj się do witryny firmy Promapp jako administrator. 

1. W menu u góry kliknij **administratora**. 
   
    ![Usługa Azure AD logowanie jednokrotne][12]

1. Kliknij pozycję **Konfiguruj**. 
   
    ![Usługa Azure AD logowanie jednokrotne][13]

1. Na **zabezpieczeń** okno dialogowe, należy wykonać następujące czynności:
   
    ![Usługa Azure AD logowanie jednokrotne][14]
    
    a. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, skopiowanej w witrynie Azure portal do **adres URL logowania jednokrotnego logowania** pola tekstowego.
    
    b. Jako **logowania jednokrotnego — tryb logowania jednokrotnego**, wybierz opcję **opcjonalnie**, a następnie kliknij przycisk **Zapisz**.

    > [!NOTE]
    > **Opcjonalnie** tryb jest tylko do celów testowych. Gdy jesteś zadowolony z konfiguracji, wybierz **wymagane** trybu, aby wymusić wszystkim użytkownikom na uwierzytelnianie za pomocą usługi Azure AD.

    c. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość certyfikatu, ale nie pierwszy wiersz (---**rozpocząć certyfikatu**---) i ostatni wiersz (---**END CERTIFICATE**---), Wklej  **Certyfikat x.509 logowania jednokrotnego** pola tekstowego, a następnie kliknij przycisk **Zapisz**.
        
> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/promapp-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/promapp-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/promapp-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/promapp-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-promapp-test-user"></a>Tworzenie użytkownika testowego Promapp

Aplikacja Promapp obsługuje Just-in-Time inicjowania obsługi administracyjnej. Oznacza to konto użytkownika jest tworzone automatycznie w razie potrzeby podczas próby uzyskania dostępu do aplikacji za pomocą panelu dostępu.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Promapp.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Promapp, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Promapp**.

    ![Konfigurowanie logowania jednokrotnego](./media/promapp-tutorial/tutorial_promapp_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Aby przetestować aplikację w **SP** zainicjowano tryb, konieczne będzie zainicjuje uwierzytelnianie z lokacji Promapp. Można to zrobić, klikając przycisk "Zaloguj się przy użyciu logowania jednokrotnego" na stronie logowania przy jednoczesnym **opcjonalnie** jest włączony tryb.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promapp-tutorial/tutorial_general_01.png
[2]: ./media/promapp-tutorial/tutorial_general_02.png
[3]: ./media/promapp-tutorial/tutorial_general_03.png
[4]: ./media/promapp-tutorial/tutorial_general_04.png
[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/promapp-tutorial/tutorial_general_100.png

[200]: ./media/promapp-tutorial/tutorial_general_200.png
[201]: ./media/promapp-tutorial/tutorial_general_201.png
[202]: ./media/promapp-tutorial/tutorial_general_202.png
[203]: ./media/promapp-tutorial/tutorial_general_203.png

