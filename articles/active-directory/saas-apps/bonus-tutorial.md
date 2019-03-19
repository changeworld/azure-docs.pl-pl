---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Bonusly | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6c82d6a8cf2281ebd77c9880903f8f20505f52c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901155"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Bonusly

W tym samouczku dowiesz się, jak zintegrować Bonusly w usłudze Azure Active Directory (Azure AD).

Integrowanie Bonusly z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Bonusly
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Bonusly (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Bonusly, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Bonusly logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Bonusly z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-bonusly-from-the-gallery"></a>Dodawanie Bonusly z galerii
Aby skonfigurować integrację Bonusly w usłudze Azure AD, należy dodać Bonusly z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Bonusly z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Bonusly**, wybierz opcję **Bonusly** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Bonusly na liście wyników](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Bonusly w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Bonusly do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Bonusly musi można ustanowić.

W Bonusly, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Bonusly, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Bonusly](#create-a-bonusly-test-user)**  — aby odpowiednikiem Britta Simon w Bonusly połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Bonusly.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Bonusly, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Bonusly** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. Na **Bonusly domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Bonusly domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/bonus-tutorial/tutorial_bonusly_url.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej Bonusly](https://bonus.ly/contact) można uzyskać wartość.
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość z certyfikatu.

    ![Link do pobierania certyfikatu](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/bonus-tutorial/tutorial_general_400.png)

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

    d. Wklej **odcisk palca** wartość kopiowana z witryny Azure portal do **odcisk palca certyfikatu** pola tekstowego.
   
1. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](./media/bonus-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/bonus-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk Dodaj](./media/bonus-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Okno dialogowe Użytkownik](./media/bonus-tutorial/create_aaduser_04.png) 

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

1. Kliknij pozycję **Ustawienia**.
 
    ![Ustawienia](./media/bonus-tutorial/ic781041.png "Ustawienia")

1. Kliknij przycisk **użytkowników i premie** kartę.
   
    ![Użytkownicy i premie](./media/bonus-tutorial/ic781042.png "użytkowników i dodatki")

1. Kliknij przycisk **Zarządzanie użytkownikami**.
   
    ![Zarządzanie użytkownikami](./media/bonus-tutorial/ic781043.png "Zarządzanie użytkownikami")

1. Kliknij pozycję **Add User** (Dodaj użytkownika).
   
    ![Dodawanie użytkownika](./media/bonus-tutorial/ic781044.png "Dodawanie użytkownika")

1. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
   
    ![Dodawanie użytkownika](./media/bonus-tutorial/ic781045.png "Dodawanie użytkownika")  

    a. W polu tekstowym **First name (Imię)** wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.
 
    c. W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon\@contoso.com**.

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
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

