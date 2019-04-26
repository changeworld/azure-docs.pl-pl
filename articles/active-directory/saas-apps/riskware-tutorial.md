---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Riskware | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3de8422efdbb7802e0532503e2733518e8050f6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60349304"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Riskware

W tym samouczku dowiesz się, jak zintegrować Riskware w usłudze Azure Active Directory (Azure AD).

Integrowanie Riskware z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Riskware.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Riskware (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Riskware, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Riskware logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Riskware z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-riskware-from-the-gallery"></a>Dodawanie Riskware z galerii
Aby skonfigurować integrację Riskware w usłudze Azure AD, należy dodać Riskware z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Riskware z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Riskware**, wybierz opcję **Riskware** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Riskware na liście wyników](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Riskware w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Riskware do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Riskware musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Riskware, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Riskware](#create-a-riskware-test-user)**  — aby odpowiednikiem Britta Simon w Riskware połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Riskware.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Riskware, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Riskware** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. Na **Riskware domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Riskware domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    | Środowisko| Wzorzec URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PRODUKCYJNE| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL:
    
    | Środowisko| Wzorzec URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników| `https://riskcloud.net/uat` |
    | PRODUKCYJNE| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Riskware](mailto:support@pansoftware.com.au) można uzyskać wartość.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/riskware-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Riskware** , kliknij przycisk **skonfigurować Riskware** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie firmy Riskware jako administrator.

1. W prawym górnym rogu, kliknij polecenie **konserwacji** aby otworzyć stronę konserwacji.

    ![Obsługa konfiguracji Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie konserwacji, kliknij przycisk **uwierzytelniania**.

    ![Authen Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. W **konfiguracji uwierzytelniania** strony, wykonaj następujące czynności:

    ![Authenconfig Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Wybierz **typu** jako **SAML** do uwierzytelniania.

    b. W **kodu** polu tekstowym wpisz swój kod, takich jak AZURE_UAT.

    c. W **opis** polu tekstowym wpisz opis takich jak AZURE konfiguracji logowania jednokrotnego.

    d. W **pojedynczego logowania na stronie** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

    e. W **Wyloguj strony** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal.

    f. W **pola formularza wpis** polu tekstowym wpisz nazwę pola, które jest obecny w odpowiedzi na wpis, zawierający SAML, takich jak SAMLResponse

    g. W **nazwa tagu tożsamości XML** atrybutu typu, który zawiera unikatowy identyfikator w odpowiedzi SAML, takich jak NameID polu tekstowym.

    h. Otwórz pobrany **Xml metadanych** skopiuj certyfikat z pliku metadanych z witryny Azure portal w programie Notatnik i wklej go w **certyfikatu** textbox

    i. W **adres URL klienta** pola tekstowego, Wklej wartość **adres URL odpowiedzi**, którego można uzyskać z zespołem pomocy technicznej.

    j. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator**, którego można uzyskać z zespołem pomocy technicznej.

    > [!Note]
    > Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Riskware](mailto:support@pansoftware.com.au) do uzyskania tych wartości.

    k. Wybierz **wpisu użyj** pola wyboru.

    l. Wybierz **żądania języka SAML użyj** pola wyboru.

    m. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/riskware-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/riskware-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/riskware-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/riskware-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-riskware-test-user"></a>Tworzenie użytkownika testowego Riskware

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Riskware, musi być obsługiwana w Riskware. W Riskware Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Riskware jako Administrator zabezpieczeń.

1. W prawym górnym rogu, kliknij polecenie **konserwacji** aby otworzyć stronę konserwacji. 

    ![Przechowuje Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie konserwacji, kliknij przycisk **osób**.

    ![Konfiguracja Riskware osoby](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Wybierz **szczegóły** kartę i wykonaj następujące czynności:

    ![Szczegóły konfiguracji Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Wybierz **typ osoby** , takich jak pracownika.

    b. W **imię** polu tekstowym Wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwisko** polu tekstowym Wprowadź nazwisko użytkownika, takich jak **Simon**.

1. Na **zabezpieczeń** karcie, wykonaj następujące czynności:

    ![Riskware konfiguracji zabezpieczeń](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. W obszarze **uwierzytelniania** zaznacz **uwierzytelniania** trybu, który ma ustawienia, takie jak konfiguracji platformy AZURE dla logowania jednokrotnego.

    b. W obszarze **szczegóły logowania** sekcji w **identyfikator użytkownika** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon\@contoso.com**.

    c. W **hasło** polu tekstowym Wprowadź hasło użytkownika.

1. Na **organizacji** karcie, wykonaj następujące czynności:

    ![Konfiguracja Riskware organizacji](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Wybierz opcję jako **Level1** organizacji.

    b. W obszarze **podstawowy obszar roboczy osoby** sekcji w **lokalizacji** polu tekstowym wpisz swoją lokalizację.

    c. W obszarze **pracowników** zaznacz **stanu** dorywczy, takich jak.

1. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Riskware.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Riskware, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Riskware**.

    ![Link Riskware na liście aplikacji](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Riskware w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Riskware.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

