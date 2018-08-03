---
title: 'Samouczek: Integracja usługi Azure Active Directory z Evernote | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 7a9282f5418737b583e29d99893df3fc81f52955
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442636"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Samouczek: Integracja usługi Azure Active Directory z Evernote

W tym samouczku dowiesz się, jak zintegrować Evernote w usłudze Azure Active Directory (Azure AD).

Integrowanie Evernote z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do połączenia z usługą Evernote.
- Aby umożliwić użytkownikom automatycznie pobrać podpisane w przypadku połączenia z usługą Evernote (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Evernote, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Evernote logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Evernote z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-evernote-from-the-gallery"></a>Dodawanie Evernote z galerii
Aby skonfigurować integrację Evernote w usłudze Azure AD, należy dodać Evernote z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Evernote z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Evernote**, wybierz opcję **Evernote** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Evernote na liście wyników](./media/evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Evernote w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Evernote do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Evernote musi można ustanowić.

W Evernote, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Evernote, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Evernote](#create-an-evernote-test-user)**  — aby odpowiednikiem Britta Simon w Evernote połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Evernote.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Evernote, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Evernote** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/evernote-tutorial/tutorial_evernote_samlbase.png)

1. Na **Evernote domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![Evernote domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/evernote-tutorial/tutorial_evernote_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL: `https://www.evernote.com/saml2`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Evernote domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/evernote-tutorial/tutorial_evernote_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://www.evernote.com/Login.action`   

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/evernote-tutorial/tutorial_evernote_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/evernote-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Evernote** , kliknij przycisk **skonfigurować Evernote** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Evernote](./media/evernote-tutorial/tutorial_evernote_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Evernote, jako administrator.

1. Przejdź do **"Konsola administracyjna"**

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Z **"Konsola administracyjna"**, przejdź do **"Zabezpieczenia"** i wybierz **"Logowanie jednokrotne"**

    ![Ustawienia logowania jednokrotnego](./media/evernote-tutorial/tutorial_evernote_sso.png)

1. Skonfiguruj następujące wartości:

    ![Ustawienia certyfikatu](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Włącz logowanie Jednokrotne:** rejestracji Jednokrotnej jest domyślnie włączona (kliknij **Wyłącz logowanie jednokrotne** Aby usunąć wymaganie logowania jednokrotnego)

    b. Wklej **SAML logowania jednokrotnego adres URL usługi** wartości, które zostały skopiowane z witryny Azure portal do **adresu URL żądania HTTP SAML** pola tekstowego.

    c. Otwórz certyfikat pobrany z usługi Azure AD w programie Notatnik i skopiuj zawartość, w tym "BEGIN CERTIFICATE" a "END CERTIFICATE" i wklej go w **certyfikat X.509** pola tekstowego. 

    d.Click **Zapisz zmiany**

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/evernote-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/evernote-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/evernote-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/evernote-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-evernote-test-user"></a>Tworzenie użytkownika testowego Evernote

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Evernote, musi być obsługiwana w Evernote.  
W przypadku Evernote Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Evernote jako administrator.

1. Kliknij przycisk **"Konsola administracyjna"**.

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Z **"Konsola administracyjna"**, przejdź do **"Dodawanie użytkowników"**.

    ![Dodaj testUser](./media/evernote-tutorial/create_aaduser_0001.png)

1. **Dodawanie członków zespołu** w **E-mail** polu tekstowym wpisz adres e-mail konta użytkownika i kliknij przycisk **zaprosić.**

    ![Dodaj testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
1. Po wysłaniu zaproszenia, właściciel konta usługi Azure Active Directory zostanie wysłana wiadomość e-mail o zaakceptowanie zaproszenia.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu z usługą Evernote.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Evernote, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Evernote**.

    ![Link Evernote na liście aplikacji](./media/evernote-tutorial/tutorial_evernote_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Evernote w panelu dostępu, użytkownik powinien uzyskać zalogowanych do aplikacji Evernote. Można będzie można logować się jako organizacji konta jednak trzeba zalogować się przy użyciu konta osobistego. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/evernote-tutorial/tutorial_general_01.png
[2]: ./media/evernote-tutorial/tutorial_general_02.png
[3]: ./media/evernote-tutorial/tutorial_general_03.png
[4]: ./media/evernote-tutorial/tutorial_general_04.png

[100]: ./media/evernote-tutorial/tutorial_general_100.png

[200]: ./media/evernote-tutorial/tutorial_general_200.png
[201]: ./media/evernote-tutorial/tutorial_general_201.png
[202]: ./media/evernote-tutorial/tutorial_general_202.png
[203]: ./media/evernote-tutorial/tutorial_general_203.png

