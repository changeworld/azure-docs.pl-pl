---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Fluxx Labs | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c48e41318ff5ba189e4cc8b8529bb3b81911052
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204393"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Fluxx Labs

W tym samouczku dowiesz się, jak integrować laboratoria Fluxx za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie laboratoriów Fluxx z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do laboratoriów Fluxx.
- Użytkowników, aby automatycznie uzyskać zalogowanych do laboratoriów Fluxx (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Fluxx Labs, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Laboratoria Fluxx logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Fluxx laboratoria z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-fluxx-labs-from-the-gallery"></a>Dodawanie Fluxx laboratoria z galerii
Aby skonfigurować integrację Fluxx laboratoriów w usłudze Azure AD, należy dodać Fluxx laboratoria z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Fluxx laboratoria z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Fluxx Labs**, wybierz opcję **Fluxx Labs** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Laboratoria Fluxx na liście wyników](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Labs Fluxx w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w laboratoriach Fluxx do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w laboratoriach Fluxx musi zostać ustanowione.

W laboratoriach Fluxx przypisze się wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Fluxx Labs, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Fluxx Labs](#create-a-fluxx-labs-test-user)**  — aby odpowiednikiem Britta Simon w laboratoriach Fluxx, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Fluxx Labs.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą Fluxx Labs, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Fluxx Labs** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

1. Na **Fluxx Labs domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Fluxx Labs domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołu pomocy technicznej Fluxx Labs](mailto:travis@fluxxlabs.com) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/fluxxlabs-tutorial/tutorial_general_400.png)

1. Na **Fluxx Labs konfiguracji** kliknij **skonfiguruj laboratoria Fluxx** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Fluxx Labs.

1. Wybierz **administratora** poniżej **ustawienia** sekcji.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config1.png)

1. W panelu administracyjnym wybierz **wtyczek** > **integracje** , a następnie wybierz **SAML SSO-(Disabled)**

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config2.png)

1. W sekcji atrybut wykonaj następujące czynności:

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Wybierz **logowania jednokrotnego SAML** pola wyboru.

    b. W **ścieżkę żądania** polu tekstowym wpisz **saml/auth/**.

    c. W **ścieżki wywołania zwrotnego** polu tekstowym wpisz **/auth/saml/callback**.

    d. W **Url(Single Sign-On URL) usługi konsumenta potwierdzenie** polu tekstowym wprowadź **adres URL odpowiedzi** wartości, które zostały wprowadzone w witrynie Azure portal.

    e. W **odbiorców (identyfikator jednostki SP)** polu tekstowym wprowadź **identyfikator** wartości, które zostały wprowadzone w witrynie Azure portal.

    f. W **adres URL docelowej logowania jednokrotnego dostawcy tożsamości** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

    g. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości** pola tekstowego.

    h. W **identyfikator nazwy formatu** polu tekstowym wprowadź wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Gdy zawartość zapisane, pole zostanie wyświetlona pusta dla zabezpieczeń, ale wartość został zapisany w konfiguracji.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/fluxxlabs-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/fluxxlabs-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/fluxxlabs-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/fluxxlabs-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Tworzenie użytkownika testowego Fluxx Labs

Aby umożliwić użytkownikom usługi Azure AD zalogować się do laboratoriów Fluxx, musi być obsługiwana laboratoriów Fluxx. W przypadku laboratoriów Fluxx aprowizacji to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Fluxx Labs jako administrator.

1. Kliknij poniżej wyświetlana **ikonę**.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config6.png)

1. Na pulpicie nawigacyjnym kliknij poniżej wyświetlone ikonę, aby otworzyć **nowych osób** karty.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config4.png)

1. Na **nowych osób** sekcji, wykonaj następujące czynności:

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Laboratoria Fluxx za pomocą adresu e-mail jako unikatowy identyfikator logowania jednokrotnego logowania. Wypełnij **UID logowania jednokrotnego** pole za pomocą adresu e-mail użytkownika, który jest zgodny z adresem e-mail i używają podczas logowania przy użyciu logowania jednokrotnego.

    b. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do laboratoriów Fluxx za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Fluxx Labs, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Fluxx Labs**.

    ![Link Fluxx Labs na liście aplikacji](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Fluxx Labs w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Fluxx Labs.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/fluxxlabs-tutorial/tutorial_general_203.png
