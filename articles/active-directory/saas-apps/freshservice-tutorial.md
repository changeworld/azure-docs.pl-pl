---
title: 'Samouczek: Integracja usługi Azure Active Directory z Freshservice | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.openlocfilehash: eb848ede258d8d25d4734664bd500235f34359e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440664"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Samouczek: Integracja usługi Azure Active Directory z Freshservice

W tym samouczku dowiesz się, jak zintegrować Freshservice w usłudze Azure Active Directory (Azure AD).

Integrowanie Freshservice z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Freshservice
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Freshservice (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Freshservice, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Freshservice logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Freshservice z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-freshservice-from-the-gallery"></a>Dodawanie Freshservice z galerii
Aby skonfigurować integrację Freshservice w usłudze Azure AD, należy dodać Freshservice z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Freshservice z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Freshservice**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/tutorial_freshservice_search.png)

1. W panelu wyników wybierz **Freshservice**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Freshservice w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Freshservice do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Freshservice musi można ustanowić.

W Freshservice, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Freshservice, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Freshservice](#creating-a-freshservice-test-user)**  — aby odpowiednikiem Britta Simon w Freshservice połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Freshservice.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Freshservice, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Freshservice** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/freshservice-tutorial/tutorial_freshservice_samlbase.png)

1. Na **Freshservice domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/freshservice-tutorial/tutorial_freshservice_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<democompany>.freshservice.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Freshservice](https://support.freshservice.com/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/freshservice-tutorial/tutorial_freshservice_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/freshservice-tutorial/tutorial_general_400.png)

1. Na **Freshservice konfigurację** , kliknij przycisk **skonfigurować Freshservice** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/freshservice-tutorial/tutorial_freshservice_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Freshservice.

1. W menu u góry kliknij **administratora**.
   
    ![Administrator](./media/freshservice-tutorial/ic790814.png "administratora")

1. W **Customer Portal**, kliknij przycisk **zabezpieczeń**.
   
    ![Zabezpieczenia](./media/freshservice-tutorial/ic790815.png "zabezpieczeń")

1. W **zabezpieczeń** sekcji, wykonaj następujące czynności:
   
    ![Logowanie jednokrotne](./media/freshservice-tutorial/ic790816.png "logowanie jednokrotne")
   
    a. Przełącznik **logowanie jednokrotne**.

    b. Wybierz **logowania jednokrotnego SAML**.

    c. W **adres URL logowania języka SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    d. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. W **odcisk palca certyfikatu zabezpieczeń** pola tekstowego, Wklej **odcisk PALCA** wartość certyfikatu, który skopiowano z witryny Azure portal.

    f. Kliknij pozycję **Zapisz**

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/freshservice-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-freshservice-test-user"></a>Tworzenie użytkownika testowego Freshservice

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do FreshService, musi być obsługiwana w FreshService. W przypadku FreshService Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **FreshService** witryny firmy jako administrator.

1. W menu u góry kliknij **administratora**.
   
    ![Administrator](./media/freshservice-tutorial/ic790814.png "administratora")

1. W **Zarządzanie użytkownikami** kliknij **jednostek żądających**.
   
    ![Jednostek żądających](./media/freshservice-tutorial/ic790818.png "stron żądających certyfikatów")

1. Kliknij przycisk **nowe zleceniodawcy**.
   
    ![Nowych jednostek żądających](./media/freshservice-tutorial/ic790819.png "nowych stron żądających certyfikatów")

1. W **nowe zleceniodawcy** sekcji, wykonaj następujące czynności:
   
    ![Nowe zleceniodawcy](./media/freshservice-tutorial/ic790820.png "nowe żądającego")   

    a. Wprowadź **imię** i **E-mail** atrybuty prawidłowego konta usługi Azure Active Directory do aprowizowania w powiązanych pól tekstowych.

    b. Kliknij pozycję **Zapisz**.
   
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail, w tym link do potwierdzenia konta, zanim stanie się aktywny
    >  

>[!NOTE]
>Można użyć jakichkolwiek innych FreshService użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez FreshService do aprowizacji kont użytkowników usługi AAD.
>  

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Freshservice, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Freshservice**.

    ![Konfigurowanie logowania jednokrotnego](./media/freshservice-tutorial/tutorial_freshservice_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Freshservice w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Freshservice.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshservice-tutorial/tutorial_general_01.png
[2]: ./media/freshservice-tutorial/tutorial_general_02.png
[3]: ./media/freshservice-tutorial/tutorial_general_03.png
[4]: ./media/freshservice-tutorial/tutorial_general_04.png

[100]: ./media/freshservice-tutorial/tutorial_general_100.png

[200]: ./media/freshservice-tutorial/tutorial_general_200.png
[201]: ./media/freshservice-tutorial/tutorial_general_201.png
[202]: ./media/freshservice-tutorial/tutorial_general_202.png
[203]: ./media/freshservice-tutorial/tutorial_general_203.png

