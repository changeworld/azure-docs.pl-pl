---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą portalu osobistych Mimecast | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i osobiste Portal Mimecast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: 88952d09672fb4dd2d31878d56a59cb425f53de2
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850452"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą portalu osobistych Mimecast

W tym samouczku dowiesz się, jak zintegrować Mimecast Portal osobistych z usługą Azure Active Directory (Azure AD).

Integrowanie portalu osobistych Mimecast z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do portalu osobistych Mimecast.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Mimecast Portal osobiste (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą portalu osobistych Mimecast, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Portal osobistych Mimecast logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie osobistych Portal Mimecast z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Dodawanie osobistych Portal Mimecast z galerii
Aby skonfigurować integrację portalu osobistych Mimecast w usłudze Azure AD, należy dodać Portal osobistych Mimecast z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Portal osobistych Mimecast z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Mimecast osobistych Portal**, wybierz opcję **Portal osobistych Mimecast** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Portal osobistych Mimecast na liście wyników](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą portalu osobistych Mimecast, oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w portalu osobistych Mimecast do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w portalu osobistych Mimecast musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą portalu osobistych Mimecast, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Portal osobistych Mimecast](#create-a-mimecast-personal-portal-test-user)**  — aby odpowiednikiem Britta Simon w portalu osobistych Mimecast, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Portal osobistych Mimecast.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą portalu osobistych Mimecast, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Portal osobistych Mimecast** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

1. Na **Mimecast osobistych Portal domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Mimecast osobistych Portal domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL: 

    | Region  |  Wartość | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Platformy        | `https://jer-api.mimecast.com/login/saml`|

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    | Region  |  Wartość | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australia       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Platformy        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL: 

    | Region  |  Wartość | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Platformy        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > Wartość identyfikatora nie jest prawdziwe. Zaktualizuj wartość za pomocą rzeczywisty identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej osobiste klienta Portal Mimecast](https://www.mimecast.com/customer-success/technical-support/) można uzyskać wartość. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/mimecast-personal-portal-tutorial/tutorial_general_400.png)

1. Na **Mimecast osobistych Konfiguracja portalu** , kliknij przycisk **skonfigurować Portal osobistych Mimecast** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja portalu Mimecast osobiste](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do portalu osobistych Mimecast, jako administrator.

1. Przejdź do **usług \> aplikacji**.
   
    ![Aplikacje](./media/mimecast-personal-portal-tutorial/ic794998.png "aplikacji")

1. Kliknij przycisk **profile uwierzytelniania**.
   
    ![Profile uwierzytelniania](./media/mimecast-personal-portal-tutorial/ic794999.png "profile uwierzytelniania")

1. Kliknij przycisk **nowy profil uwierzytelniania**.
   
    ![Nowy profil uwierzytelniania](./media/mimecast-personal-portal-tutorial/ic795000.png "nowy profil uwierzytelniania")

1. W **profilu uwierzytelniania** sekcji, wykonaj następujące czynności:
   
    ![Profilu uwierzytelniania](./media/mimecast-personal-portal-tutorial/ic795001.png "profilu uwierzytelniania")
   
    a. W **opis** polu tekstowym wpisz nazwę dla danej konfiguracji.
   
    b. Wybierz **wymusić uwierzytelnianie SAML portalu osobiste Mimecast**.
   
    c. Jako **dostawcy**, wybierz opcję **usługi Azure Active Directory**.
   
    d. W **adres URL wystawcy** pola tekstowego, Wklej wartość **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal.
   
    e. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.
   
    f. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    g. Otwórz swoje **base-64** zakodowanego certyfikatu w programie Notatnik pobrane z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości (metadane)** pola tekstowego.

    h. Wybierz **Zezwalaj na logowanie na**.
   
    i. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/mimecast-personal-portal-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/mimecast-personal-portal-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/mimecast-personal-portal-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Tworzenie użytkownika testowego Mimecast osobistych portalu

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do portalu osobistych Mimecast, musi być obsługiwana w portalu osobistych Mimecast. W przypadku Mimecast osobistych portalu inicjowania obsługi administracyjnej jest zadanie ręczne.

Należy zarejestrować domeny, aby można było utworzyć użytkowników.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się na swoje **Portal osobistych Mimecast** jako administrator.

1. Przejdź do **katalogi \> wewnętrzny**.
   
    ![Katalogi](./media/mimecast-personal-portal-tutorial/ic795003.png "katalogów")

1. Kliknij przycisk **zarejestrować nową domenę**.
   
    ![Zarejestruj nową domenę](./media/mimecast-personal-portal-tutorial/ic795004.png "zarejestrować nową domenę")

1. Po utworzeniu nowej domeny, kliknij przycisk **nowy adres**.
   
    ![Nowy adres](./media/mimecast-personal-portal-tutorial/ic795005.png "nowy adres")

1. W oknie dialogowym Nowy adres wykonaj następujące kroki Azure prawidłowe konto usługi AD, które chcesz aprowizować:
   
    ![Zapisz](./media/mimecast-personal-portal-tutorial/ic795006.png "Zapisz")
   
    a. W **adres E-mail** polu tekstowym wpisz **adres E-mail** użytkownika jako **BrittaSimon@contoso.com**.
    
    b. W **globalne nazwę** polu tekstowym wpisz **username** jako **BrittaSimon**.

    c. W **hasło**, i **Potwierdź hasło** pola tekstowe, wpisz **hasło** użytkownika.
   
    b. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika portalu osobistych Mimecast lub interfejsów API udostępnionych przez Portal osobistych Mimecast.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do portalu osobistych Mimecast za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Mimecast osobistych Portal, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Portal osobistych Mimecast**.

    ![Link Mimecast Portal osobistych na liście aplikacji](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mimecast Portal osobiste w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji osobistych Portal Mimecast.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-personal-portal-tutorial/tutorial_general_203.png

