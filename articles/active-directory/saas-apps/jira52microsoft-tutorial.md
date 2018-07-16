---
title: 'Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 84afd45f6fb56222aa6d2dea063251353f406b26
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042680"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Samouczek: Integracja usługi Azure Active Directory z logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)

W tym samouczku dowiesz się, jak zintegrować logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) w usłudze Azure Active Directory (Azure AD).

Integracja logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2).
- Użytkowników, aby automatycznie uzyskać zalogowanych do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Opis

Korzystać z serwera Atlassian JIRA konta Microsoft Azure Active Directory, aby włączyć logowanie jednokrotne. W ten sposób wszystkich użytkowników w Twojej organizacji można użyć poświadczeń usługi Azure AD można zalogować się do aplikacji w usłudze JIRA. Ta wtyczka używa języka SAML 2.0 dla Federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- JIRA Core i 5.2 oprogramowania powinny zainstalowany i skonfigurowany na Windows 64-bitowej wersji
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
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

**Obsługiwane wersje:**

*   JIRA Core i oprogramowania: 5.2
*   JIRA obsługuje również 6.0 i 7.8. Aby uzyskać więcej informacji, kliknij przycisk [logowania jednokrotnego SAML JIRA przez firmę Microsoft](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii
Aby skonfigurować integrację logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) w usłudze Azure AD, należy dodać logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)**, wybierz opcję **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) na liście wyników](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w usłudze JIRA logowania jednokrotnego SAML przez firmy Microsoft (w wersji 5.2) dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika w usłudze JIRA logowania jednokrotnego SAML przez firmy Microsoft (w wersji 5.2) musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Utwórz logowania jednokrotnego SAML JIRA przez użytkownika testu firmy Microsoft (programu w wersji 5.2)](#create-a-jira-saml-sso-by-microsoft-v52-test-user)**  — aby odpowiednikiem Britta Simon w logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2) połączoną usługę Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w logowania jednokrotnego SAML w swojej JIRA aplikacji firmy Microsoft (programu w wersji 5.2).

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), wykonaj następujące czynności:**

1. W witrynie Azure portal na **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_samlbase.png)

3. Na **logowania jednokrotnego SAML JIRA Domain firmy Microsoft, a adresy URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i Microsoft Domain pojedynczego logowania jednokrotnego informacje, logowania jednokrotnego SAML programu JIRA](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Port jest opcjonalny w przypadku, gdy jest o nazwie adresu URL. Te wartości są odbierane podczas konfigurowania wtyczki programu Jira, które zostało wyjaśnione w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Konfigurowanie logowania jednokrotnego](./media/jira52microsoft-tutorial/tutorial_metadataurl.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/jira52microsoft-tutorial/tutorial_general_400.png)

6. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator z wystąpieniem programu JIRA.

7. Umieść kursor na koła zębatego, a następnie kliknij przycisk **dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/jira52microsoft-tutorial/addon1.png)

8. W sekcji Karta dodatki, kliknij przycisk **zarządzać dodatkami**.

    ![Konfigurowanie logowania jednokrotnego](./media/jira52microsoft-tutorial/addon7.png)

9. Pobierz wtyczkę z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56521). Ręcznie przekazać wtyczkę udostępniane przez Microsoft za pomocą **przekazywanie dodatku** menu. Pobieranie wtyczki jest objęta [Umowa serwisowa usługi Microsoft](https://www.microsoft.com/en-us/servicesagreement/).

    ![Konfigurowanie logowania jednokrotnego](./media/jira52microsoft-tutorial/addon12.png)

10. Po zainstalowaniu dodatku plug-in pojawia się w **użytkownik zainstalował** sekcji dodatków. Kliknij przycisk **Konfiguruj** do skonfigurowania nowej wtyczki.

    ![Konfigurowanie logowania jednokrotnego](./media/jira52microsoft-tutorial/addon13.png)

11. Wykonaj poniższe czynności na stronie konfiguracji:

    ![Konfigurowanie logowania jednokrotnego](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Upewnij się, że istnieje tylko jeden certyfikat mapowany aplikację tak, aby nie było błędu w rozwiązaniu metadanych. Jeśli dostępnych jest wiele certyfikatów podczas rozpoznawania metadane, błąd otrzymuje administrator.

    a. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartość skopiowaną z witryny Azure portal i kliknij przycisk **rozwiązać** przycisku. Odczytuje adres URL metadanych dostawcy tożsamości i wypełni wszystkie informacje dotyczące pól.

    b. Kopiuj **identyfikator, adres URL odpowiedzi i adres URL logowania** wartości, a następnie wklej je w **identyfikator, adres URL odpowiedzi i adres URL logowania** pola tekstowe odpowiednio w **logowania jednokrotnego SAML JIRA przez firmy Microsoft (programu w wersji 5.2), domena i adresy URL**  sekcji w witrynie Azure portal.

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

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/jira52microsoft-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/jira52microsoft-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/jira52microsoft-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/jira52microsoft-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-jira-saml-sso-by-microsoft-v52-test-user"></a>Utwórz logowania jednokrotnego SAML JIRA przez użytkownika testu firmy Microsoft (programu w wersji 5.2)

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do programu JIRA na lokalnym serwerze, musi być obsługiwana w usłudze JIRA na serwerze lokalnym.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej JIRA na lokalnym serwerze jako administrator.

2. Umieść kursor na koła zębatego, a następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user1.png)

3. Nastąpi przekierowanie do strony dostępu administratora, aby wprowadzić **hasło** i kliknij przycisk **Potwierdź** przycisku.

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user2.png)

4. W obszarze **Zarządzanie użytkownikami** kliknij sekcję pozycję **Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user3.png) 

5. Na **"Utwórz nowego użytkownika"** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/jira52microsoft-tutorial/user4.png)

    a. W **adres E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    b. W **imię i nazwisko** pole tekstowe, wpisz pełną nazwę użytkownika, takich jak Britta Simon.

    c. W **Username** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    d. W **hasło** pole tekstowe, wpisz hasło użytkownika.

    e. Kliknij przycisk **Utwórz użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2).

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon do logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2), wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **logowania jednokrotnego SAML JIRA przez firmy Microsoft (w wersji 5.2)**.

    ![Logowania jednokrotnego SAML JIRA przez łącze firmy Microsoft (programu w wersji 5.2) na liście aplikacji](./media/jira52microsoft-tutorial/tutorial_singlesign-onforjira5.2_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu logowania jednokrotnego SAML JIRA przez firmy Microsoft (programu w wersji 5.2) kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do logowania jednokrotnego SAML w swojej JIRA przez aplikację Microsoft (programu w wersji 5.2).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/msaadssojira5.2-tutorial/tutorial_general_01.png
[2]: ./media/msaadssojira5.2-tutorial/tutorial_general_02.png
[3]: ./media/msaadssojira5.2-tutorial/tutorial_general_03.png
[4]: ./media/msaadssojira5.2-tutorial/tutorial_general_04.png

[100]: ./media/msaadssojira5.2-tutorial/tutorial_general_100.png

[200]: ./media/msaadssojira5.2-tutorial/tutorial_general_200.png
[201]: ./media/msaadssojira5.2-tutorial/tutorial_general_201.png
[202]: ./media/msaadssojira5.2-tutorial/tutorial_general_202.png
[203]: ./media/msaadssojira5.2-tutorial/tutorial_general_203.png