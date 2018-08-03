---
title: 'Samouczek: Integracja usługi Azure Active Directory z Sprinklr | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: e2dc9b7e7cf5964c36b21418a0162c1c2ef92dc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430185"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Samouczek: Integracja usługi Azure Active Directory z Sprinklr

W tym samouczku dowiesz się, jak zintegrować Sprinklr w usłudze Azure Active Directory (Azure AD).

Integrowanie Sprinklr z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Sprinklr
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Sprinklr (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Sprinklr, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sprinklr logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Sprinklr z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sprinklr-from-the-gallery"></a>Dodawanie Sprinklr z galerii
Aby skonfigurować integrację Sprinklr w usłudze Azure AD, należy dodać Sprinklr z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Sprinklr z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Sprinklr**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

1. W panelu wyników wybierz **Sprinklr**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Sprinklr w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Sprinklr do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Sprinklr musi można ustanowić.

W Sprinklr, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Sprinklr, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Sprinklr](#creating-a-sprinklr-test-user)**  — aby odpowiednikiem Britta Simon w Sprinklr połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Sprinklr.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Sprinklr, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Sprinklr** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

1. Na **Sprinklr domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.sprinklr.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj wartość za pomocą rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Sprinklr](https://www.sprinklr.com/contact-us/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/sprinklr-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Sprinklr** , kliknij przycisk **skonfigurować Sprinklr** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Sprinklr.

1. Przejdź do **administracji \> ustawienia**.
   
    ![Administracja](./media/sprinklr-tutorial/ic782907.png "administracji")

1. Przejdź do **zarządzania partnera \> jednokrotnego** w okienku po lewej stronie.
   
    ![Zarządzanie partnerem](./media/sprinklr-tutorial/ic782908.png "zarządzania partnera")

1. Kliknij przycisk **+ dodatki jednokrotnego**.
   
    ![Pojedynczy znak Ons](./media/sprinklr-tutorial/ic782909.png "pojedynczy znak Ons")

1. Na **funkcji logowania jednokrotnego** strony, wykonaj następujące czynności:
   
    ![Pojedynczy znak Ons](./media/sprinklr-tutorial/ic782910.png "pojedynczy znak Ons")

    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład: *WAADSSOTest*).

    b. Wybierz **włączone**.

    c. Wybierz **użycia nowego certyfikatu logowania jednokrotnego**.
             
    e. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości** pola tekstowego.

    f. Wklej **identyfikator jednostki SAML** wartość, które zostały skopiowane z witryny Azure Portal do **identyfikator jednostki** pola tekstowego.

    g. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartość, które zostały skopiowane z witryny Azure Portal do **adres URL logowania dostawcy tożsamości** pola tekstowego.

    h. Wklej **adres URL wylogowania** wartość, które zostały skopiowane z witryny Azure Portal do **adres URL wylogowania dostawcy tożsamości** pola tekstowego.
     
    i. Jako **typ Identyfikatora użytkownika SAML**, wybierz opcję **potwierdzenie zawiera użytkownika "s sprinklr.com username**.

    j. Jako **lokalizacji identyfikator użytkownika SAML**, wybierz opcję **identyfikator użytkownika ma identyfikator nazwy elementu instrukcji podmiotu**.

    k. Kliknij pozycję **Zapisz**.
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-sprinklr-test-user"></a>Tworzenie użytkownika testowego Sprinklr

1. Zaloguj się do witryny firmy Sprinklr jako administrator.

1. Przejdź do **administracji \> ustawienia**.
   
    ![Administracja](./media/sprinklr-tutorial/ic782907.png "administracji")

1. Przejdź do **klienta zarządzania \> użytkowników** z okienka po lewej stronie.
   
    ![Ustawienia](./media/sprinklr-tutorial/ic782914.png "ustawienia")

1. Kliknij przycisk **Dodaj użytkownika**.
   
    ![Ustawienia](./media/sprinklr-tutorial/ic782915.png "ustawienia")

1. Na **Edycja użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
    ![Edytuj użytkownika](./media/sprinklr-tutorial/ic782916.png "Edytuj użytkownika") 

    a. W **E-mail**, **imię** i **nazwisko** pola tekstowe, wpisz informacje, które chcesz aprowizować konta użytkownika usługi Azure AD.

    b. Wybierz **wyłączone hasło**.

    c. Wybierz **języka**.

    d. Wybierz **typ użytkownika**.

    e. Kliknij przycisk **aktualizacji**.
   
     >[!IMPORTANT]
     >**Wyłączone hasło** należy wybrać, aby użytkownik mógł zalogować się za pośrednictwem dostawcy tożsamości. 
     
1. Przejdź do **roli**, a następnie wykonaj następujące czynności:
   
    ![Partner role](./media/sprinklr-tutorial/ic782917.png "partnera ról")

    a. Z **Global** listy wybierz **wszystkich\_uprawnienia**.  

    b. Kliknij przycisk **aktualizacji**.

>[!NOTE]
>Można użyć jakichkolwiek innych Sprinklr użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Sprinklr można uaktywniać ich konta usługi Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Sprinklr.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Sprinklr, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Sprinklr**.

    ![Konfigurowanie logowania jednokrotnego](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Sprinklr w panelu dostępu, należy uzyskać automatycznie zalogowanych do aplikacji Sprinklr Aby dowiedzieć się więcej o panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

