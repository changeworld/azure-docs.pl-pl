---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi GitHub | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 7f932346cdfd6363bd76f8167b8598d3a1199de3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427792"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi GitHub

W tym samouczku dowiesz się, jak zintegrować usługi GitHub z usługi Azure Active Directory (Azure AD).

Integrowanie usługi GitHub z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi GitHub.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do usługi GitHub (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi GitHub, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- GitHub logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi GitHub za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-github-from-the-gallery"></a>Dodawanie usługi GitHub za pomocą galerii
Aby skonfigurować integrację z usługi GitHub do usługi Azure AD, należy dodać GitHub z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi GitHub z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **GitHub**, wybierz opcję **GitHub** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![GitHub na liście wyników](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi GitHub, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze GitHub dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze GitHub musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi GitHub, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego GitHub](#create-a-github-test-user)**  — aby odpowiednikiem Britta Simon w GitHub, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi GitHub.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą usługi GitHub, wykonaj następujące czynności:**

1. W witrynie Azure portal na **GitHub** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/github-tutorial/tutorial_github_samlbase.png)

1. Na **GitHub domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi GitHub i adresy URL pojedynczego logowania jednokrotnego informacji](./media/github-tutorial/tutorial_github_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://github.com/orgs/<entity-id>/sso`

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Należy pamiętać, że nie są rzeczywiste wartości. Musisz zaktualizować te wartości przy użyciu rzeczywistego konta dla adresu URL i identyfikator. W tym miejscu zalecamy przy użyciu unikatowej wartości ciągu w identyfikatorze. Przejdź do sekcji administratora usługi GitHub, aby pobrać te wartości.

1. Na **atrybutów użytkownika** zaznacz **identyfikator użytkownika** jako user.mail.

    ![Konfigurowanie logowania jednokrotnego](./media/github-tutorial/tutorial_github_attribute_new01.png)

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/github-tutorial/tutorial_github_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/github-tutorial/tutorial_general_400.png)

1. Na **konfiguracji usługi GitHub** kliknij **Konfigurowanie usługi GitHub** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja usługi GitHub](./media/github-tutorial/tutorial_github_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny organizacji GitHub, jako administrator.

1. Przejdź do **ustawienia** i kliknij przycisk **zabezpieczeń**

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_03.png)

1. Sprawdź **uwierzytelnianie SAML Włącz** okno, odsłaniając pola konfiguracji logowania jednokrotnego. Następnie użyj pojedynczego logowania jednokrotnego wartość adresu URL, aby zaktualizować pojedynczy adres URL logowania w konfiguracji usługi Azure AD.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_13.png)

1. Określ następujące pola:

    a. W **adres URL logowania** pola tekstowego, Wklej **pojedynczego SAML logowania jednokrotnego adres URL usługi** wartości, które zostały skopiowane z witryny Azure portal.

    b. W **wystawcy** pola tekstowego, Wklej **identyfikator jednostki SAML** wartości, które zostały skopiowane z witryny Azure portal.

    c. Otwórz certyfikat pobrany z witryny Azure portal w programie Notatnik, Wklej zawartość **certyfikatu publicznego** pola tekstowego.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_051.png)

1. Kliknij pozycję **plik konfiguracji SAML testu** do upewnij się, że żadne błędy sprawdzania poprawności ani błędy podczas logowania jednokrotnego.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_06.png)

1. Kliknij pozycję **Zapisz**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/github-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/github-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/github-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/github-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-github-test-user"></a>Tworzenie użytkownika testowego usługi GitHub

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w usłudze GitHub. Usługa GitHub obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](github-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do witryny usługi GitHub firmy jako administrator.

1. Kliknij przycisk **osób**.

    ![Osoby](./media/github-tutorial/tutorial_github_config_github_08.png "osoby")

1. Kliknij przycisk **Członkowskie zaproszenia**.

    ![Zaproś użytkowników](./media/github-tutorial/tutorial_github_config_github_09.png "zaprosić użytkowników")

1. Na **Członkowskie zaproszenia** okna dialogowego strony, wykonaj następujące czynności:

    a. W **E-mail** pole tekstowe, wpisz adres e-mail konta Britta Simon.

    ![Zaproś inne osoby](./media/github-tutorial/tutorial_github_config_github_10.png "zapraszać inne osoby")

    b. Kliknij przycisk **wysłać zaproszenia**.

    ![Zaproś inne osoby](./media/github-tutorial/tutorial_github_config_github_11.png "zapraszać inne osoby")

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory będzie otrzymywać wiadomości e-mail i kliknij link, aby potwierdzić swoje konto, zanim stanie się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi GitHub.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon GitHub, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **GitHub**.

    ![Link usługi GitHub, na liście aplikacji](./media/github-tutorial/tutorial_github_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi GitHub, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację usługi GitHub.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png

