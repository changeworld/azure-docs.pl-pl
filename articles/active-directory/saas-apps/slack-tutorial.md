---
title: 'Samouczek: Integracja usługi Azure Active Directory z Slack | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8f79926d0d4729c6ad939bc604e9eb885dbe9f03
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421264"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Samouczek: Integracja usługi Azure Active Directory z Slack

W tym samouczku dowiesz się, jak zintegrować Slack przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie Slack z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Slack
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Slack (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Slack, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Slack logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Slack z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-slack-from-the-gallery"></a>Dodawanie Slack z galerii
Aby skonfigurować integrację programu Slack w usłudze Azure AD, należy dodać Slack z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Slack z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Slack**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/tutorial_slack_search.png)

1. W panelu wyników wybierz **Slack**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Slack, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Slack do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Slack musi można ustanowić.

W Slack, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD jednokrotne logowanie w usłudze Slack, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Slack](#creating-a-slack-test-user)**  — aby odpowiednikiem Britta Simon w Slack, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Slack.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Slack, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Slack** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. Na **Slack, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.slack.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `https://slack.com`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Musisz zaktualizować wartości z rzeczywistych na adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Slack](https://slack.com/help/contact) można uzyskać wartość.
     
1. Slack aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Jeśli istnieją użytkownicy, którzy są przypisani **adres e-mail** nie znajduje się na licencję usługi Office 365 **User.Email** oświadczenia nie będą widoczne w tokenu SAML. W takich przypadkach zalecane jest używanie **user.userprincipalname** jako **User.Email** wartość, aby zamapować jako atrybutu **Unikatowy identyfikator** zamiast tego.

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, wybierz opcję **user.mail** jako **identyfikator użytkownika** i dla każdego wiersza wyświetlane w poniższej tabeli, wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | User.mail |  
    | User.Username | user.userprincipalname |

    a. Kliknij pozycję **atrybut** otworzyć **Edytuj atrybut** okna dialogowego pole, a następnie wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    b. Z **wartość** wybierz wartość atrybutu wyświetlanego dla tego wiersza.

    c. Pozostaw **Namespace** puste.

    d. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Slack** , kliknij przycisk **skonfigurować Slack** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_configure.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Slack.

1. Przejdź do **usługi Microsoft Azure AD** następnie przejdź do **ustawienia zespołu**.

     ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/slack-tutorial/tutorial_slack_001.png)

1. W **ustawienia zespołu** kliknij **uwierzytelniania** kartę, a następnie kliknij przycisk **Zmień ustawienia**.

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/slack-tutorial/tutorial_slack_002.png)

1. Na **ustawienia uwierzytelniania SAML** okno dialogowe, należy wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/slack-tutorial/tutorial_slack_003.png)

    a.  W **SAML 2.0 punktu końcowego (HTTP)** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    b.  W **wystawca dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML identyfikator jednostki**, które zostały skopiowane z witryny Azure portal.

    c.  Otwórz plik pobranego certyfikatu w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu publicznego** pola tekstowego.

    d. Skonfiguruj trzy powyższe ustawienia odpowiednie dla Twojego zespołu Slack. Aby uzyskać więcej informacji na temat ustawień można znaleźć **Przewodnik po konfiguracji logowania jednokrotnego w Slack** tutaj. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Kliknij przycisk **Zapisz konfigurację**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_03.png)

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/slack-tutorial/create_aaduser_04.png)

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="creating-a-slack-test-user"></a>Tworzenie użytkownika testowego Slack

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Slack. Slack obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Slack, jeśli go jeszcze nie istnieje. Slack obsługuje również automatyczna aprowizacja użytkowników, więcej szczegółów można znaleźć [tutaj](slack-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

> [!NOTE]
> Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Slack.

![Przypisz użytkownika][200]

**Aby przypisać Britta Simon Slack, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Slack**.

    ![Konfigurowanie logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Slack w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Slack.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
