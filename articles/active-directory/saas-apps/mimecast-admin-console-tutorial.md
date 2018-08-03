---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą konsoli administracyjnej Mimecast | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Mimecast konsoli administracyjnej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: ce4142c5b4a20886a94c87699f262f7238fc2cb4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438573"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą konsoli administracyjnej Mimecast

W tym samouczku dowiesz się, jak zintegrować Mimecast konsoli administracyjnej w usłudze Azure Active Directory (Azure AD).

Integracja konsoli administracyjnej Mimecast z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do konsoli administracyjnej Mimecast.
- Użytkowników, aby automatycznie uzyskać zalogowanych do konsoli administracyjnej Mimecast (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą konsoli administracyjnej Mimecast, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Konsola administracyjna Mimecast logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Mimecast konsoli administracyjnej z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Dodawanie Mimecast konsoli administracyjnej z galerii
Aby skonfigurować integrację z konsoli administracyjnej Mimecast w usłudze Azure AD, należy dodać Mimecast konsoli administracyjnej z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Mimecast konsoli administracyjnej z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Mimecast konsoli administracyjnej**, wybierz opcję **Mimecast konsoli administracyjnej** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Konsola administracyjna Mimecast na liście wyników](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą konsoli administracyjnej Mimecast w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w konsoli administracyjnej Mimecast do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w konsoli administracyjnej Mimecast musi zostać ustanowione.

W konsoli administracyjnej Mimecast przypisze się wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą konsoli administracyjnej Mimecast, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego konsoli administracyjnej Mimecast](#create-a-mimecast-admin-console-test-user)**  — aby odpowiednikiem Britta Simon w Mimecast Konsola administracyjna, która jest połączona z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji konsoli administracyjnej Mimecast.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą konsoli administracyjnej Mimecast, wykonaj następujące czynności:**

1. W witrynie Azure portal na **konsoli administracyjnej Mimecast** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

1. Na **Mimecast Administrator Console domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Mimecast Administrator Console domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > Adres URL logowania zależy od regionu.

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/mimecast-admin-console-tutorial/tutorial_general_400.png)

1. Na **Konfiguracja konsoli administratora Mimecast** , kliknij przycisk **Konfigurowanie konsoli administracyjnej Mimecast** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfiguracja konsoli administratora Mimecast](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do konsoli administracyjnej usługi Mimecast, jako administrator.

1. Przejdź do **usług \> aplikacji**.

    ![Usługi](./media/mimecast-admin-console-tutorial/ic794998.png "usług")

1. Kliknij przycisk **profile uwierzytelniania**.

    ![Profile uwierzytelniania](./media/mimecast-admin-console-tutorial/ic794999.png "profile uwierzytelniania")
    
1. Kliknij przycisk **nowy profil uwierzytelniania**.

    ![Nowe profile uwierzytelniania](./media/mimecast-admin-console-tutorial/ic795000.png "nowych profilów uwierzytelniania")

1. W **profilu uwierzytelniania** sekcji, wykonaj następujące czynności:

    ![Profilu uwierzytelniania](./media/mimecast-admin-console-tutorial/ic795015.png "profilu uwierzytelniania")
    
    a. W **opis** polu tekstowym wpisz nazwę dla danej konfiguracji.
    
    b. Wybierz **wymusić uwierzytelnianie SAML dla konsoli administracyjnej Mimecast**.
    
    c. Jako **dostawcy**, wybierz opcję **usługi Azure Active Directory**.
    
    d. Wklej **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal do **adres URL wystawcy** pole tekstowe.
    
    e. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, skopiowanej w witrynie Azure portal do **adres URL logowania** pola tekstowego.

    f. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal do **adres URL wylogowania** pole tekstowe.
    
    >[!NOTE]
    >Wartość adresu URL logowania i wartość adres URL wylogowania są przez konsolę administracyjną Mimecast takie same.
    
    g. Twój certyfikat base-64 pobrane z witryny Azure portal w programie Notatnik, Open Usuń pierwszy wiersz ("*--*") i ostatni wiersz ("*--*"), skopiuj zawartość pozostałych go do usługi Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości (metadane)** pola tekstowego.
    
    h. Wybierz **Zezwalaj na logowanie na**.
    
    i. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/mimecast-admin-console-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/mimecast-admin-console-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/mimecast-admin-console-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Tworzenie użytkownika testowego Mimecast konsoli administracyjnej

Aby umożliwić użytkownikom usługi Azure AD zalogować się do konsoli administracyjnej Mimecast, musi być obsługiwana w konsoli administracyjnej Mimecast. W przypadku konsoli administracyjnej Mimecast aprowizacji to zadanie ręczne.

* Należy zarejestrować domeny, aby można było utworzyć użytkowników.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się na swoje **konsoli administracyjnej Mimecast** jako administrator.
1. Przejdź do **katalogi \> wewnętrzny**.
   
   ![Katalogi](./media/mimecast-admin-console-tutorial/ic795003.png "katalogów")
1. Kliknij przycisk **zarejestrować nową domenę**.
   
   ![Zarejestruj nową domenę](./media/mimecast-admin-console-tutorial/ic795004.png "zarejestrować nową domenę")
1. Po utworzeniu nowej domeny, kliknij przycisk **nowy adres**.
   
   ![Nowy adres](./media/mimecast-admin-console-tutorial/ic795005.png "nowy adres")
1. W oknie dialogowym Nowy adres wykonaj następujące czynności:
   
   ![Zapisz](./media/mimecast-admin-console-tutorial/ic795006.png "Zapisz")
   
   a. Typ **adres E-mail**, **globalne nazwę**, **hasło**, i **Potwierdź hasło** atrybuty prawidłowy, usługa Azure AD konto ma być aprowizuj do powiązanych pól tekstowych.

   b. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika konsoli administracyjnej Mimecast lub interfejsów API dostarczonych przez Mimecast konsoli administracyjnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do konsoli administracyjnej Mimecast za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Mimecast konsoli administracyjnej, należy wykonać następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **konsoli administracyjnej Mimecast**.

    ![Konsola administracyjna Mimecast łącze na liście aplikacji](./media/mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mimecast konsoli administracyjnej w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji konsoli administracyjnej Mimecast.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-admin-console-tutorial/tutorial_general_203.png

