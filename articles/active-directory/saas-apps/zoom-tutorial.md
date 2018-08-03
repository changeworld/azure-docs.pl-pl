---
title: 'Samouczek: Integracja usługi Azure Active Directory z powiększenia | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i powiększenia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 57ae31245a356a4cd5769fe71ef471922bf6faf9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440138"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Samouczek: Integracja usługi Azure Active Directory z powiększenia

W tym samouczku dowiesz się, jak zintegrować powiększenia w usłudze Azure Active Directory (Azure AD).

Integrowanie powiększenia z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do powiększania.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do powiększenia (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą powiększenia, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Powiększenie logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie powiększenia z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zoom-from-the-gallery"></a>Dodawanie powiększenia z galerii
Aby skonfigurować integrację powiększenia w usłudze Azure AD, należy dodać powiększenia z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać powiększenia z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **powiększenia**, wybierz opcję **powiększenia** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Powiększenie na liście wyników](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą powiększenia, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w powiększenia do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w powiększenia musi można ustanowić.

Powiększenie, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą powiększenia, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego powiększenia](#create-a-zoom-test-user)**  — aby odpowiednikiem Britta Simon w powiększenia, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji powiększenia.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z powiększenia, wykonaj następujące czynności:**

1. W witrynie Azure portal na **powiększenia** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

1. Na **powiększenia domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena i adresy URL pojedynczego logowania jednokrotnego informacji powiększenia](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.zoom.us`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `<companyname>.zoom.us`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta powiększenia](https://support.zoom.us/hc) do uzyskania tych wartości.

1. Aplikacja powiększenia oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. 

    ![Konfigurowanie logowania jednokrotnego](./media/zoom-tutorial/tutorial_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Wartość Namespace |
    | ------------------- | -----------|--------- |    
    | Email address (Adres e-mail) | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Imię | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Nazwisko | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Numer telefonu | User.telephonenumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | Dział | User.Department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. W **Namespace** polu tekstowym wpisz wartość przestrzeni nazw wyświetlanego dla tego wiersza.
    
    e. Kliknij przycisk **OK**. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/zoom-tutorial/tutorial_zoom_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/zoom-tutorial/tutorial_general_400.png)

1. Na **powiększenia konfiguracji** , kliknij przycisk **skonfigurować powiększenia** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja powiększenia](./media/zoom-tutorial/tutorial_zoom_configure.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy powiększenia.

1. Kliknij przycisk **logowania jednokrotnego** kartę.
   
    ![Karta rejestracji jednokrotnej](./media/zoom-tutorial/IC784700.png "logowanie jednokrotne")

1. Kliknij przycisk **zabezpieczeniem** kartę, a następnie przejdź do **logowania jednokrotnego** ustawienia.

1. W sekcji logowanie jednokrotne wykonaj następujące czynności:
   
    ![Pojedynczy znak w sekcji](./media/zoom-tutorial/IC784701.png "logowanie jednokrotne")
   
    a. W **adres URL logowania strony** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    b. W **adres URL strony wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
     
    c. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości** pola tekstowego.

    d. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal. 

    e. Kliknij pozycję **Zapisz**.

    > [!NOTE] 
    > Aby uzyskać więcej informacji odwiedź stronę dokumentacji powiększenia [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zoom-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/zoom-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zoom-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zoom-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-zoom-test-user"></a>Tworzenie użytkownika testowego powiększenia

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się, aby powiększyć, musi być obsługiwana w powiększenia. W przypadku powiększenie Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojej **powiększenia** witryny firmy jako administrator.
 
1. Kliknij przycisk **zarządzania kontami** kartę, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

1. W sekcji Zarządzanie użytkownikami kliknij **dodawania użytkowników**.
   
    ![Zarządzanie użytkownikami](./media/zoom-tutorial/IC784703.png "Zarządzanie użytkownikami")

1. Na **dodawania użytkowników** strony, wykonaj następujące czynności:
   
    ![Dodawanie użytkowników](./media/zoom-tutorial/IC784704.png "Dodawanie użytkowników")
   
    a. Jako **typ użytkownika**, wybierz opcję **podstawowe**.

    b. W **wiadomości E-mail** pole tekstowe, wpisz adres e-mail prawidłowy, usługa Azure AD konto do aprowizowania.

    c. Kliknij pozycję **Add** (Dodaj).

> [!NOTE]
> Można użyć jakichkolwiek innych powiększenia użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez powiększenia do świadczenia usługi Azure Active Directory, kont użytkowników.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do powiększania.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon, aby powiększyć, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **powiększenia**.

    ![Link powiększenia na liście aplikacji](./media/zoom-tutorial/tutorial_zoom_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka powiększenia w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji powiększenia.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zoom-tutorial/tutorial_general_01.png
[2]: ./media/zoom-tutorial/tutorial_general_02.png
[3]: ./media/zoom-tutorial/tutorial_general_03.png
[4]: ./media/zoom-tutorial/tutorial_general_04.png

[100]: ./media/zoom-tutorial/tutorial_general_100.png

[200]: ./media/zoom-tutorial/tutorial_general_200.png
[201]: ./media/zoom-tutorial/tutorial_general_201.png
[202]: ./media/zoom-tutorial/tutorial_general_202.png
[203]: ./media/zoom-tutorial/tutorial_general_203.png

