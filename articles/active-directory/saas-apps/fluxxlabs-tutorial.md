---
title: 'Samouczek: Integracja usługi Azure Active Directory z laboratoriów Fluxx | Dokumentacja firmy Microsoft'
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
ms.openlocfilehash: 7bb15323a1f4ead02a24c4ee451de8890a635032
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053051"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Fluxx Labs

W tym samouczku dowiesz się, jak integrować laboratoria Fluxx za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie laboratoriów Fluxx z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do laboratoriów Fluxx.
- Użytkowników, aby automatycznie uzyskać zalogowanych do laboratoriów Fluxx (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Fluxx Labs, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Laboratoria Fluxx logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Fluxx laboratoria z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-fluxx-labs-from-the-gallery"></a>Dodawanie Fluxx laboratoria z galerii
Aby skonfigurować integrację Fluxx laboratoriów w usłudze Azure AD, należy dodać Fluxx laboratoria z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Fluxx laboratoria z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Fluxx Labs**, wybierz opcję **Fluxx Labs** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Laboratoria Fluxx na liście wyników](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Labs Fluxx w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w laboratoriach Fluxx do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w laboratoriach Fluxx musi zostać ustanowione.

W laboratoriach Fluxx przypisze się wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Fluxx Labs, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Fluxx Labs](#create-a-fluxx-labs-test-user)**  — aby odpowiednikiem Britta Simon w laboratoriach Fluxx, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Fluxx Labs.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą Fluxx Labs, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Fluxx Labs** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. Na **Fluxx Labs domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Fluxx Labs domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołu pomocy technicznej Fluxx Labs](mailto:travis@fluxxlabs.com) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/fluxxlabs-tutorial/tutorial_general_400.png)

6. Na **Fluxx Labs konfiguracji** kliknij **skonfiguruj laboratoria Fluxx** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Fluxx Labs.

8. Wybierz **administratora** poniżej **ustawienia** sekcji.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config1.png)

9. W panelu administracyjnym wybierz **wtyczek** > **integracje** , a następnie wybierz **SAML SSO-(Disabled)**

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config2.png)

10. W sekcji atrybut wykonaj następujące czynności:

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

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/fluxxlabs-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/fluxxlabs-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/fluxxlabs-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Tworzenie użytkownika testowego Fluxx Labs

Aby umożliwić użytkownikom usługi Azure AD zalogować się do laboratoriów Fluxx, musi być obsługiwana laboratoriów Fluxx. W przypadku laboratoriów Fluxx aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Fluxx Labs jako administrator.

2. Kliknij poniżej wyświetlana **ikonę**.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config6.png)

3. Na pulpicie nawigacyjnym kliknij poniżej wyświetlone ikonę, aby otworzyć **nowych osób** karty.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config4.png)

4. Na **nowych osób** sekcji, wykonaj następujące czynności:

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Laboratoria Fluxx za pomocą adresu e-mail jako unikatowy identyfikator logowania jednokrotnego logowania. Wypełnij **UID logowania jednokrotnego** pole za pomocą adresu e-mail użytkownika, który jest zgodny z adresem e-mail i używają podczas logowania przy użyciu logowania jednokrotnego.

    b. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do laboratoriów Fluxx za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Fluxx Labs, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Fluxx Labs**.

    ![Link Fluxx Labs na liście aplikacji](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Fluxx Labs w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Fluxx Labs.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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
