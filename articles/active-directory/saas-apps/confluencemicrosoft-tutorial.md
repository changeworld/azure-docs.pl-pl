---
title: 'Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML Confluence przez firmę Microsoft | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego SAML Confluence przez firmę Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeedes
ms.openlocfilehash: 2e254faae0289cd00c7e66d430ec3148fccb364a
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288156"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML Confluence przez firmę Microsoft

W tym samouczku dowiesz się, jak zintegrować logowania jednokrotnego SAML Confluence przez firmę Microsoft za pomocą usługi Azure Active Directory (Azure AD).

Integracja logowania jednokrotnego SAML Confluence przez firmę Microsoft z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego SAML Confluence przez firmę Microsoft.
- Użytkowników, aby automatycznie uzyskać zalogowanych do logowania jednokrotnego SAML Confluence przez firmę Microsoft (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="description"></a>Opis:

Korzystać z serwera Atlassian Confluence konta Microsoft Azure Active Directory, aby włączyć logowanie jednokrotne. W ten sposób wszystkich użytkowników w Twojej organizacji można używać poświadczeń usługi Azure AD do logowania do aplikacji Confluence. Ta wtyczka używa języka SAML 2.0 dla Federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą logowania jednokrotnego SAML Confluence przez firmę Microsoft, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Aplikacja serwera confluence zainstalowane na serwerze Windows 64-bitowych (lokalnie lub w chmurze infrastruktury IaaS)
- Serwer confluence jest obsługujące protokół HTTPS
- Należy pamiętać, że obsługiwane wersje dla wtyczki Confluence są wymienione w poniżej sekcji.
- Serwer confluence jest dostępny w Internecie, szczególnie do strony logowania usługi AD Azure w celu uwierzytelniania i powinien możliwość odbierania token z usługi Azure AD
- Poświadczenia administratora są konfigurowane w Confluence
- WebSudo jest wyłączona w Confluence
- Użytkownika testowego utworzone w aplikacji serwera Confluence

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego programu Confluence. Testowanie integracji najpierw w rozwoju lub środowisko przejściowe aplikacji, a następnie użyć w środowisku produkcyjnym.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Obsługiwane wersje Confluence

Aktualnie obsługiwane są następujące wersje programu Confluence:

- Confluence: 5.10 do 5.0
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4.
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie logowania jednokrotnego SAML Confluence przez firmę Microsoft z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML Confluence przez firmę Microsoft z galerii

Aby skonfigurować integrację logowania jednokrotnego SAML Confluence przez firmę Microsoft do usługi Azure AD, należy dodać logowania jednokrotnego SAML Confluence przez firmę Microsoft w galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML Confluence przez firmę Microsoft w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML Confluence przez firmę Microsoft**, wybierz opcję **logowania jednokrotnego SAML Confluence przez firmę Microsoft** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Logowania jednokrotnego SAML confluence przez firmę Microsoft na liście wyników](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML Confluence przez firmę Microsoft, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w logowania jednokrotnego SAML Confluence przez firmę Microsoft użytkownikowi w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w logowania jednokrotnego SAML Confluence przez firmę Microsoft musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML Confluence przez firmę Microsoft, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie przez program Microsoft test użytkownika, logowania jednokrotnego SAML Confluence](#creating-confluence-saml-sso-by-microsoft-test-user)**  — aby odpowiednikiem Britta Simon w logowania jednokrotnego SAML Confluence przez firmę Microsoft, która jest połączona z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w sieci logowania jednokrotnego SAML Confluence aplikacji firmy Microsoft.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML Confluence przez firmę Microsoft, wykonaj następujące czynności:**

1. W witrynie Azure portal na **przez firmę Microsoft, logowania jednokrotnego SAML Confluence** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Logowania jednokrotnego SAML confluence Domain firmy Microsoft i adresów URL pojedynczego logowania jednokrotnego informacje](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Port jest opcjonalny w przypadku, gdy jest o nazwie adresu URL. Te wartości są odbierane podczas konfigurowania wtyczki Confluence, które zostało wyjaśnione w dalszej części tego samouczka.

5. Na **certyfikat podpisywania SAML** stronie **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i Wklej je do Notatnika.

    ![Link pobierania certyfikatu](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do Twojego wystąpienia Confluence.

7. Umieść kursor na koła zębatego, a następnie kliknij przycisk **dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon1.png)

8. Pobierz wtyczkę z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Ręcznie przekazać wtyczkę udostępniane przez Microsoft za pomocą **przekazywanie dodatku** menu. Pobieranie wtyczki jest objęta [Umowa serwisowa usługi Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon12.png)

9. Po zainstalowaniu dodatku plug-in pojawia się w **użytkownik zainstalował** dodatki części **Zarządzanie dodatek** sekcji. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon13.png)

10. Wykonaj poniższe czynności na stronie konfiguracji:

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Upewnij się, że istnieje tylko jeden certyfikat mapowany aplikację tak, aby nie było błędu w rozwiązaniu metadanych. Jeśli dostępnych jest wiele certyfikatów, administrator pobiera błąd podczas rozpoznawania metadanych.

    a. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartość skopiowaną z witryny Azure portal i kliknij przycisk **rozwiązać** przycisku. Odczytuje adres URL metadanych dostawcy tożsamości i wypełni wszystkie informacje dotyczące pól.

    b. Kopiuj **identyfikator, adres URL odpowiedzi i adres URL logowania** wartości, a następnie wklej je w **identyfikator, adres URL odpowiedzi i adres URL logowania** pola tekstowe odpowiednio w **logowania jednokrotnego SAML Confluence Domain firmy Microsoft, a adresy URL**  sekcji w witrynie Azure portal.

    c. W **nazwę przycisku logowania** wpisz nazwę przycisku przez organizację nowych użytkowników na ekranie logowania.

    d. W **lokalizacje identyfikator użytkownika SAML**, wybierz opcję **identyfikator użytkownika jest w elemencie NameIdentifier instrukcji podmiotu** lub **identyfikator użytkownika jest w elemencie atrybutu**.  Ten identyfikator musi być Confluence identyfikatora użytkownika. Jeśli identyfikator użytkownika nie jest takie samo, następnie system nie pozwoli użytkownikom na logowanie. 

    > [!Note]
    > Domyślna lokalizacja SAML identyfikator użytkownika to identyfikator nazwy. Można ją zmienić na atrybutu opcji i wprowadź nazwę odpowiedniego atrybutu.
    
    e. Jeśli wybierzesz **identyfikator użytkownika jest w elemencie atrybut** opcji, a następnie w obszarze **nazwa atrybutu** polu tekstowym wpisz nazwę atrybutu, gdy jest oczekiwany identyfikator użytkownika. 

    f. Jeśli używasz domeny federacyjnej (na przykład usług AD FS itp.) z usługą Azure AD, następnie kliknij pozycję **Włączanie odnajdowania obszaru macierzystego** opcji i skonfigurować **nazwy domeny**.
    
    g. W **nazwy domeny** wpisz nazwę domeny, w tym miejscu w przypadku logowania za pomocą usług AD FS.

    h. Sprawdź **włączyć logowanie się** Jeśli chcesz się wylogować z usługi Azure AD, gdy użytkownik zaloguje z Confluence. 

    i. Kliknij przycisk **Zapisz** przycisk, aby zapisać ustawienia.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów, odwiedź stronę [podręczniku administratora łącznika logowania jednokrotnego Confluence MS](../ms-confluence-jira-plugin-adminguide.md) i dostępna jest również [— często zadawane pytania](../ms-confluence-jira-plugin-faq.md) Twojej pomocy

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-confluence-saml-sso-by-microsoft-test-user"></a>Tworzenie logowania jednokrotnego SAML Confluence przez użytkownika testu firmy Microsoft

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do serwera lokalnego Confluence, ich musi być obsługiwana do logowania jednokrotnego SAML Confluence przez firmę Microsoft. Dla logowania jednokrotnego SAML Confluence przez firmę Microsoft Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego Confluence jako administrator.

2. Umieść kursor na koła zębatego, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](./media/confluencemicrosoft-tutorial/user1.png) 

3. W sekcji Użytkownicy kliknij **dodawania użytkowników** kartę. Na **Dodawanie użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/confluencemicrosoft-tutorial/user2.png) 

    a. W **Username** pole tekstowe, wpisz adres e-mail użytkownika, takich jak Britta Simon.

    b. W **imię i nazwisko** polu tekstowym wpisz pełną nazwę użytkownika, takich jak Britta Simon.

    c. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    d. W **hasło** pole tekstowe, wpisz hasło dla Britta Simon.

    e. Kliknij przycisk **Potwierdź hasło** wprowadź ponownie hasło.

    f. Kliknij przycisk **Dodaj** przycisku.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego SAML Confluence przez firmę Microsoft.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **przez firmę Microsoft, logowania jednokrotnego SAML Confluence**.

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu logowania jednokrotnego SAML Confluence przez Microsoft kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do usługi logowania jednokrotnego SAML Confluence przez aplikację Microsoft.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
