---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logiem SSO JIRA SAML firmy Microsoft | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem JIRA SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd922d0e65da627f11e6aab3827cb848c3dd635
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75560528"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logiem SSO JIRA SAML firmy Microsoft

W tym samouczku dowiesz się, jak zintegrować JIRA SAML SSO przez firmę Microsoft z usługi Azure Active Directory (Azure AD). Po zintegrowaniu SSO JIRA SAML firmy Microsoft z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do JIRA SAML SSO przez firmę Microsoft.
* Włącz użytkownikom automatyczne logowanie do SSO JIRA SAML przez firmę Microsoft za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Opis

Użyj konta usługi Microsoft Azure Active Directory w połączeniu z serwerem Atlassian JIRA, aby umożliwić logowanie jednokrotne. W ten sposób wszyscy użytkownicy organizacji mogą używać poświadczeń usługi Azure AD do logowania się do aplikacji JIRA. Ta wtyczka używa protokołu SAML 2.0 na potrzeby federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z programem JIRA SAML SSO by Microsoft są potrzebne następujące elementy:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
- JIRA Core i Software 6.4 do 8.5.1 lub JIRA Service Desk 3.0 do 4.6.0 powinny być zainstalowane i skonfigurowane w wersji 64-bitowej systemu Windows
- Dla serwera JIRA włączono obsługę protokołu HTTPS
- Zwróć uwagę na obsługiwane wersje wtyczki usługi JIRA podane w poniżej sekcji.
- Serwer JIRA jest dostępny w Internecie, w szczególności dla strony logowania usługi AD Azure na potrzeby uwierzytelniania, i ma możliwość odebrania tokenu z usługi Azure AD
- Poświadczenia administratora są skonfigurowane w usłudze JIRA
- Funkcja WebSudo jest wyłączona w usłudze JIRA
- Użytkownik testowy został utworzony w aplikacji serwera JIRA

> [!NOTE]
> Podczas testowania kroków w tym samouczku nie zalecamy używania środowiska produkcyjnego usługi JIRA. Integrację należy najpierw przetestować w środowisku programistycznym lub przejściowym aplikacji, a dopiero później użyć środowiska produkcyjnego.

Aby rozpocząć, potrzebujesz następujących elementów:

* Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
* JIRA SAML SSO przez microsoft logowanie jednokrotne (Logowanie jednokrotne) włączone subskrypcji.

## <a name="supported-versions-of-jira"></a>Obsługiwane wersje usługi JIRA

* JIRA Core i oprogramowanie: 6.4 do 8.5.1
* JIRA Service Desk 3.0.0 do 4.6.0
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Należy pamiętać, że nasza wtyczka JIRA działa również na Ubuntu w wersji 16.04 i Linux.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Program JIRA SAML SSO by Microsoft obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**.

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Dodawanie programu JIRA SAML SSO by Microsoft z galerii

Aby skonfigurować integrację programu JIRA SAML SSO by Microsoft z usługą Azure AD, należy dodać program JIRA SAML SSO by Microsoft z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **wpisać syloga JIRA SAML przez firmę Microsoft** w polu wyszukiwania.
1. Wybierz **JIRA SAML SSO firmy Microsoft** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jira-saml-sso-by-microsoft"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla logowania jednokrotnego JIRA SAML przez firmę Microsoft

Konfigurowanie i testowanie sytua obsługi usługi Azure AD za pomocą funkcji SSO JIRA SAML firmy Microsoft przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji SSO JIRA SAML firmy Microsoft.

Aby skonfigurować i przetestować syg jako jeden na platformę Azure AD przy pomocy SSO JIRA SAML firmy Microsoft, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie SSO JIRA SAML przez firmę Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz JIRA SAML SSO przez użytkownika testowego firmy Microsoft](#create-jira-saml-sso-by-microsoft-test-user)** — aby mieć odpowiednik B.Simon w JIRA SAML SSO przez firmę Microsoft, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie logowania **SSO JIRA SAML by Microsoft(** microsoft application integration page) znajdziesz sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<domain:port>/plugins/servlet/saml/auth`

    b. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<domain:port>/`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Port jest opcjonalny, jeśli adres URL zawiera nazwę. Te wartości można uzyskać podczas konfigurowania wtyczki usługi Jira, co jest objaśnione w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do JIRA SAML SSO przez firmę Microsoft.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **JIRA SAML SSO by Microsoft**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Konfigurowanie sytów JIRA SAML przez microsoft sso

1. W innym oknie przeglądarki internetowej zaloguj się do wystąpienia JIRA jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon1.png)

3. Pobierz wtyczkę z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=56506). Ręcznie przekaż wtyczkę udostępnioną przez firmę Microsoft za pomocą menu **Przekazywanie dodatku**. Pobieranie wtyczki jest objęte [umową serwisową firmy Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon12.png)

4. Aby uruchomić scenariusz ze zwrotnym serwerem proxy lub modułem równoważenia obciążenia usługi JIRA, wykonaj następujące kroki:

    > [!NOTE]
    > Najpierw skonfiguruj serwer za pomocą poniższych instrukcji, a następnie zainstaluj wtyczkę.

    a. Dodaj poniższy atrybut w elemencie **Connector** port w pliku **server.xml** aplikacji serwera JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Zmień wartość w polu **Podstawowy adres URL** w obszarze **Ustawienia systemu** odpowiednio dla serwera proxy lub modułu równoważenia obciążenia.

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Po zainstalowaniu wtyczki jest ona wyświetlana w sekcji dodatków **Zainstalowane przez użytkownika** w ramach sekcji **Zarządzanie dodatkami**. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon14.png)

6. Wykonaj następujące kroki na stronie konfiguracji:

    ![Konfigurowanie logowania jednokrotnego](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Upewnij się, że tylko jeden certyfikat jest zamapowany do aplikacji, aby zapobiec błędom podczas rozpoznawania metadanych. Jeśli jest dostępnych wiele certyfikatów podczas rozpoznawania metadanych, administrator otrzyma błąd.

    1. W polu tekstowym **Adres URL metadanych** wklej wartość pola **Adres URL metadanych federacyjnych aplikacji** skopiowaną z witryny Azure Portal i kliknij przycisk **Rozpoznaj**. Spowoduje to odczytanie adresu URL metadanych dostawcy tożsamości i wypełnienie wszystkich pól danymi.

    1. Skopiuj wartości **identyfikatora, adresu URL odpowiedzi i adresu URL logowania**, a następnie wklej je odpowiednio w polach **Identyfikator, Adres URL odpowiedzi i Adres URL logowania** w sekcji **Domena i adresy URL programu JIRA SAML SSO by Microsoft** w witrynie Azure Portal.

    1. W polu **Nazwa przycisku logowania** wpisz nazwę przycisku, która według Twojej organizacji ma być wyświetlana użytkownikom na ekranie logowania.
    
    1. W **opisie przycisku logowania** wpisz opis przycisku, który chcesz, aby użytkownicy zobaczyli go na ekranie logowania.

    1. W **saml lokalizacje identyfikatora użytkownika** wybierz nazwę **użytkownika jest w NameIdentifier element instrukcji Podmiot** lub identyfikator użytkownika znajduje się w **attribute elementu**.  Ten identyfikator musi być identyfikatorem użytkownika JIRA. Jeśli identyfikator użytkownika nie jest dopasowany, system nie zezwoli użytkownikom na logowanie.

       > [!Note]
       > Domyślna lokalizacja identyfikatora użytkownika SAML to element NameIdentifier. Można ją zmienić za pomocą opcji elementu Attribute, podając nazwę odpowiedniego atrybutu.

    1. Jeśli wybierzesz nazwę użytkownika w opcji **elementu Atrybut,** w a następnie w **obszarze tekstowym Nazwa atrybutu** wpisz nazwę atrybutu, w którym oczekiwana jest nazwa użytkownika.

    1. Jeśli używasz domeny federacyjnej (na przykład usług ADFS itp.) w połączeniu z usługą Azure AD, kliknij opcję **Włącz odnajdywanie obszaru głównego** i skonfiguruj pole **Nazwa domeny**.

    1. W polu **Nazwa domeny** wpisz nazwę domeny w przypadku logowania za pomocą usług ADFS.

    1. Zaznacz **opcję Włącz wylogowanie jednokrotne,** jeśli chcesz wylogować się z usługi Azure AD, gdy użytkownik wyloguje się z JIRA.
    
    1. Włącz pole wyboru **Force Azure Login,** jeśli chcesz zalogować się tylko za pomocą poświadczeń usługi Azure AD.
    
       > [!Note]
       > Aby włączyć domyślny formularz logowania do logowania administratora na stronie logowania po włączeniu funkcji force azure login, dodaj parametr zapytania w adresie URL przeglądarki.
       > `https://<domain:port>/login.jsp?force_azure_login=false`

    1. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

       > [!NOTE]
       > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów, odwiedź [przewodnik administracyjny łącznika SSO ms JIRA](../ms-confluence-jira-plugin-adminguide.md). Istnieje również [często](../ms-confluence-jira-plugin-faq.md) zadawane pytania dotyczące twojej pomocy.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Tworzenie użytkownika testowego programu JIRA SAML SSO by Microsoft

Aby umożliwić użytkownikom usługi Azure AD logowanie się do serwera lokalnego JIRA, muszą one być aprowidzone do JIRA SAML SSO przez firmę Microsoft. W przypadku programu JIRA SAML SSO by Microsoft aprowizowanie to zadanie wykonywane ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do lokalnego serwera JIRA jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user1.png)

3. Nastąpi przekierowanie do strony dostępu administratora. Podaj wartość w polu **Password** (Hasło) i kliknij przycisk **Confirm** (Potwierdź).

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user2.png)

4. W sekcji karty **User management** (Zarządzanie użytkownikami) kliknij pozycję **create user** (utwórz użytkownika).

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user3.png) 

5. Na stronie okna dialogowego **„Create new user”** (Tworzenie nowego użytkownika) wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/jiramicrosoft-tutorial/user4.png) 

    a. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład B.simon@contoso.com.

    b. W polu **tekstowym Imię i nazwisko** wpisz pełną nazwę użytkownika, taką jak B.Simon.

    d. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład B.simon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. Kliknij **pozycję Utwórz użytkownika**.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka JIRA SAML SSO by Microsoft na panelu dostępu powinno nastąpić automatyczne zalogowanie do programu JIRA SAML SSO by Microsoft, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj SSO JIRA SAML firmy Microsoft z usługą Azure AD](https://aad.portal.azure.com/)
