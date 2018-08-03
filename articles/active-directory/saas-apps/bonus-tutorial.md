---
title: 'Samouczek: Integracja usługi Azure Active Directory z Bonusly | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 175c00d36491fbf43149aef9a590219b330581c1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422981"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Samouczek: Integracja usługi Azure Active Directory z Bonusly

W tym samouczku dowiesz się, jak zintegrować Bonusly w usłudze Azure Active Directory (Azure AD).

Integrowanie Bonusly z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Bonusly
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Bonusly (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Bonusly, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Bonusly logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Bonusly z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-bonusly-from-the-gallery"></a>Dodawanie Bonusly z galerii
Aby skonfigurować integrację Bonusly w usłudze Azure AD, należy dodać Bonusly z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Bonusly z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Bonusly**, wybierz opcję **Bonusly** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Bonusly na liście wyników](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Bonusly w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Bonusly do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Bonusly musi można ustanowić.

W Bonusly, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Bonusly, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Bonusly](#create-a-bonusly-test-user)**  — aby odpowiednikiem Britta Simon w Bonusly połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Bonusly.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Bonusly, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Bonusly** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. Na **Bonusly domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Bonusly domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/bonus-tutorial/tutorial_bonusly_url.png)

    W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj wartość za pomocą rzeczywisty adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej Bonusly](https://bonus.ly/contact) można uzyskać wartość.
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość z certyfikatu.

    ![Link pobierania certyfikatu](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/bonus-tutorial/tutorial_general_400.png)

1. Na **Bonusly konfiguracji** kliknij **skonfigurować Bonusly** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Bonusly konfiguracji](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

1. W oknie innej przeglądarki, zaloguj się do Twojej **Bonusly** dzierżawy.

1. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie wybierz pozycję **integracje i aplikacje**.
   
    ![Bonusly sekcji społecznościowych](./media/bonus-tutorial/ic773686.png "Bonusly")
1. W obszarze **logowania jednokrotnego**, wybierz opcję **SAML**.

1. Na **SAML** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Bonusly strony okna dialogowego Saml](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. W **logowania jednokrotnego dostawcy tożsamości, docelowy adres URL** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.
   
    b. W **wystawca dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal. 

    c. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    d. Wklej **odcisk palca** wartość kopiowana z witryny Azure portal do **odcisk palca certyfikatu** pola tekstowego.
   
1. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/bonus-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    !["Użytkownicy i grupy" i "All users" linki](./media/bonus-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk Dodaj](./media/bonus-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/bonus-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-bonusly-test-user"></a>Tworzenie użytkownika testowego Bonusly

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Bonusly, musi być obsługiwana w Bonusly. W przypadku Bonusly Inicjowanie obsługi administracyjnej jest zadanie ręczne.

>[!NOTE]
>Można użyć jakichkolwiek innych Bonusly użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Bonusly do aprowizacji kont użytkowników usługi AAD.
>  

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. W oknie przeglądarki sieci web Zaloguj się do Bonusly dzierżawy.

1. Kliknij przycisk **ustawienia**.
 
    ![Ustawienia](./media/bonus-tutorial/ic781041.png "ustawienia")

1. Kliknij przycisk **użytkowników i premie** kartę.
   
    ![Użytkownicy i premie](./media/bonus-tutorial/ic781042.png "użytkowników i dodatki")

1. Kliknij przycisk **Zarządzanie użytkownikami**.
   
    ![Zarządzanie użytkownikami](./media/bonus-tutorial/ic781043.png "Zarządzanie użytkownikami")

1. Kliknij przycisk **Dodaj użytkownika**.
   
    ![Dodaj użytkownika](./media/bonus-tutorial/ic781044.png "Dodaj użytkownika")

1. Na **Dodaj użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
    ![Dodaj użytkownika](./media/bonus-tutorial/ic781045.png "Dodaj użytkownika")  

    a. W **imię** polu tekstowym Wprowadź imię użytkownika, takich jak **Britta**.

    b. W **nazwisko** polu tekstowym Wprowadź nazwisko użytkownika, takich jak **Simon**.
 
    c. W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    d. Kliknij pozycję **Zapisz**.
   
     >[!NOTE]
     >Właściciel konta usługi Azure AD, otrzymuje wiadomość e-mail zawierającą link do potwierdzenia konta, zanim stanie się aktywny.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Bonusly.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Bonusly, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Bonusly**.

    ![Bonusly łącze na liście aplikacji](./media/bonus-tutorial/tutorial_bonusly_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Bonusly w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowanych do Bonusly aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

