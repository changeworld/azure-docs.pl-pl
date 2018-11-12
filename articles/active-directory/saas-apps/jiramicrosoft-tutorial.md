---
title: 'Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML JIRA przez firmę Microsoft | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego SAML JIRA przez firmę Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: jeedes
ms.openlocfilehash: f3f7fc3b837dd4eef9bab8ff34a36329436bad9a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010732"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML JIRA przez firmę Microsoft

W tym samouczku dowiesz się, jak zintegrować logowania jednokrotnego SAML JIRA przez firmę Microsoft za pomocą usługi Azure Active Directory (Azure AD).

Integracja logowania jednokrotnego SAML JIRA przez firmę Microsoft z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego SAML JIRA przez firmę Microsoft.
- Użytkowników, aby automatycznie uzyskać zalogowanych do logowania jednokrotnego SAML JIRA przez firmę Microsoft (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="description"></a>Opis

Korzystać z serwera Atlassian JIRA konta Microsoft Azure Active Directory, aby włączyć logowanie jednokrotne. W ten sposób wszystkich użytkowników w Twojej organizacji można użyć poświadczeń usługi Azure AD można zalogować się do aplikacji w usłudze JIRA. Ta wtyczka używa języka SAML 2.0 dla Federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą logowania jednokrotnego SAML JIRA przez firmę Microsoft, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Core programu JIRA i 6.0 oprogramowania do 7.12 lub JIRA usługi technicznej 3.0, 3.5 należy zainstalowany i skonfigurowany na Windows 64-bitowej wersji
- Serwer programu JIRA jest obsługujące protokół HTTPS
- Należy pamiętać, że obsługiwane wersje dla wtyczki programu JIRA są wymienione w poniżej sekcji.
- Serwer programu JIRA jest dostępny w Internecie, szczególnie do strony logowania usługi AD Azure w celu uwierzytelniania i powinien możliwość odbierania token z usługi Azure AD
- Poświadczenia administratora są konfigurowane w usłudze JIRA
- WebSudo jest wyłączona w usłudze JIRA
- Użytkownika testowego utworzone w usłudze JIRA aplikacji serwera

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używanie środowiska produkcyjnego programu JIRA. Testowanie integracji najpierw w rozwoju lub środowisko przejściowe aplikacji, a następnie użyć w środowisku produkcyjnym.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Obsługiwane wersje programu JIRA

* JIRA Core i oprogramowania: 6.0 do 7.12
* JIRA pomoc techniczna 3.0.0 lub 3.5.0
* JIRA obsługuje również 5.2. Aby uzyskać więcej informacji, kliknij przycisk [Microsoft Azure Active Directory logowania jednokrotnego dla 5.2 programu JIRA](jira52microsoft-tutorial.md)

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie logowania jednokrotnego SAML JIRA przez firmę Microsoft z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML JIRA przez firmę Microsoft z galerii

Aby skonfigurować integrację logowania jednokrotnego SAML JIRA przez firmę Microsoft do usługi Azure AD, należy dodać logowania jednokrotnego SAML JIRA przez firmę Microsoft w galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML JIRA przez firmę Microsoft w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML JIRA przez firmę Microsoft**, wybierz opcję **logowania jednokrotnego SAML JIRA przez firmę Microsoft** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Logowania jednokrotnego SAML JIRA przez firmę Microsoft na liście wyników](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML JIRA przez firmę Microsoft, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w logowania jednokrotnego SAML JIRA przez firmę Microsoft użytkownikowi w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w logowania jednokrotnego SAML JIRA przez firmę Microsoft musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML JIRA przez firmę Microsoft, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie logowania jednokrotnego SAML JIRA przez użytkownika testowego Microsoft](#creating-jira-saml-sso-by-microsoft-test-user)**  — aby odpowiednikiem Britta Simon w przez firmę Microsoft, która jest połączona z usługi Azure AD reprezentacja użytkownika, logowania jednokrotnego SAML programu JIRA.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w logowania jednokrotnego SAML w swojej JIRA aplikacji firmy Microsoft.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML JIRA przez firmę Microsoft, wykonaj następujące czynności:**

1. W witrynie Azure portal na **przez firmę Microsoft, logowania jednokrotnego SAML JIRA** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Adresy URL i Microsoft Domain pojedynczego logowania jednokrotnego informacje, logowania jednokrotnego SAML programu JIRA](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Port jest opcjonalny w przypadku, gdy jest o nazwie adresu URL. Te wartości są odbierane podczas konfigurowania wtyczki programu Jira, które zostało wyjaśnione w dalszej części tego samouczka.

5. Na **certyfikat podpisywania SAML** stronie **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link pobierania certyfikatu](./media/jiramicrosoft-tutorial/tutorial_metadataurl.png) 

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator z wystąpieniem programu JIRA.

7. Umieść kursor na koła zębatego, a następnie kliknij przycisk **dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon1.png)

8. Pobierz wtyczkę z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). Ręcznie przekazać wtyczkę udostępniane przez Microsoft za pomocą **przekazywanie dodatku** menu. Pobieranie wtyczki jest objęta [Umowa serwisowa usługi Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon12.png)

9. Po zainstalowaniu dodatku plug-in pojawia się w **użytkownik zainstalował** dodatki części **Zarządzanie dodatek** sekcji. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon13.png)

10. Wykonaj poniższe czynności na stronie konfiguracji:

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Upewnij się, że istnieje tylko jeden certyfikat mapowany aplikację tak, aby nie było błędu w rozwiązaniu metadanych. Jeśli dostępnych jest wiele certyfikatów podczas rozpoznawania metadane, błąd otrzymuje administrator.

    a. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartość skopiowaną z witryny Azure portal i kliknij przycisk **rozwiązać** przycisku. Odczytuje adres URL metadanych dostawcy tożsamości i wypełni wszystkie informacje dotyczące pól.

    b. Kopiuj **identyfikator, adres URL odpowiedzi i adres URL logowania** wartości, a następnie wklej je w **identyfikator, adres URL odpowiedzi i adres URL logowania** pola tekstowe odpowiednio w **Domain firmy Microsoft, a adresy URL,logowaniajednokrotnegoSAMLprogramuJIRA** sekcji w witrynie Azure portal.

    c. W **nazwę przycisku logowania** wpisz nazwę przycisku przez organizację nowych użytkowników na ekranie logowania.

    d. W **lokalizacje identyfikator użytkownika SAML** wybierz opcję **identyfikator użytkownika jest w elemencie NameIdentifier instrukcji podmiotu** lub **identyfikator użytkownika jest w elemencie atrybutu**.  Ten identyfikator musi być identyfikatorem użytkownika programu JIRA. Jeśli identyfikator użytkownika nie jest takie samo, następnie system nie pozwoli użytkownikom na logowanie.

    > [!Note]
    > Domyślna lokalizacja SAML identyfikator użytkownika to identyfikator nazwy. Można ją zmienić na atrybutu opcji i wprowadź nazwę odpowiedniego atrybutu.

    e. Jeśli wybierzesz **identyfikator użytkownika jest w elemencie atrybut** opcji, a następnie w obszarze **nazwa atrybutu** polu tekstowym wpisz nazwę atrybutu, gdy jest oczekiwany identyfikator użytkownika.

    f. Jeśli używasz domeny federacyjnej (na przykład usług AD FS itp.) z usługą Azure AD, następnie kliknij pozycję **Włączanie odnajdowania obszaru macierzystego** opcji i skonfigurować **nazwy domeny**.

    g. W **nazwy domeny** wpisz nazwę domeny, w tym miejscu w przypadku logowania za pomocą usług AD FS.

    h. Sprawdź **włączyć logowanie się** Jeśli chcesz się wylogować z usługi Azure AD, gdy użytkownik zaloguje z programu JIRA.

    i. Kliknij przycisk **Zapisz** przycisk, aby zapisać ustawienia.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów, odwiedź stronę [podręczniku administratora łącznika logowania jednokrotnego JIRA MS](../ms-confluence-jira-plugin-adminguide.md) i dostępna jest również [— często zadawane pytania](../ms-confluence-jira-plugin-faq.md) Twojej pomocy

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

### <a name="creating-jira-saml-sso-by-microsoft-test-user"></a>Tworzenie logowania jednokrotnego SAML JIRA przez użytkownika testu firmy Microsoft

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do programu JIRA na serwerze lokalnym, ich musi być obsługiwana do logowania jednokrotnego SAML JIRA przez firmę Microsoft. JIRA SAML logowania jednokrotnego przez firmę Microsoft Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej JIRA na lokalnym serwerze jako administrator.

2. Umieść kursor na koła zębatego, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user1.png)

3. Nastąpi przekierowanie do strony dostępu administratora, aby wprowadzić **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user2.png)

4. W obszarze **Zarządzanie użytkownikami** kliknij sekcję pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user3.png) 

5. Na **"Utwórz nowego użytkownika"** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user4.png) 

    a. W **adres E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    b. W **imię i nazwisko** pole tekstowe, wpisz pełną nazwę użytkownika, takich jak Britta Simon.

    c. W **Username** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    d. W **hasło** pole tekstowe, wpisz hasło użytkownika.

    e. Kliknij przycisk **Utwórz użytkownika**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego SAML JIRA przez firmę Microsoft.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **przez firmę Microsoft, logowania jednokrotnego SAML JIRA**.

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu logowania jednokrotnego SAML JIRA przez Microsoft kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do logowania jednokrotnego SAML w swojej JIRA przez aplikację Microsoft.
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
