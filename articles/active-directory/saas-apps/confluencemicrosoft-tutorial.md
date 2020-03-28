---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logowania sytuacyjnego SAML z confluence firmy Microsoft | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Confluence SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6ea52474f3b352fabf19050f20012438e9d0c19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120594"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logowania SSO SAML z confluence przez firmę Microsoft

W tym samouczku dowiesz się, jak zintegrować sso SSO SAML confluence przez firmę Microsoft z usługą Azure Active Directory (Azure AD). Po zintegrowaniu sso SSO SAML confluence firmy Microsoft z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do confluence SAML SSO przez firmę Microsoft.
* Włącz użytkownikom automatyczne logowanie do confluence SAML SSO przez firmę Microsoft za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Opis:

Użyj konta usługi Microsoft Azure Active Directory w połączeniu z serwerem Atlassian Confluence, aby umożliwić logowanie jednokrotne. W ten sposób wszyscy użytkownicy organizacji mogą używać poświadczeń usługi Azure AD do logowania się do aplikacji Confluence. Ta wtyczka używa protokołu SAML 2.0 na potrzeby federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Confluence SAML SSO by Microsoft są potrzebne następujące elementy:

- Subskrypcji usługi Azure AD
- Aplikacja serwera Confluence zainstalowana na serwerze z 64-bitowym systemem Windows (lokalnie lub w infrastrukturze IaaS chmury)
- Serwer Confluence obsługuje protokół HTTPS
- Zwróć uwagę na obsługiwane wersje wtyczki Confluence podane w poniższej sekcji.
- Serwer Confluence jest dostępny w Internecie, w szczególności dla strony logowania usługi Azure AD na potrzeby uwierzytelniania, i powinien mieć możliwość odbierania tokenu z usługi Azure AD
- Poświadczenia administratora są skonfigurowane w aplikacji Confluence
- WebSudo jest wyłączone w aplikacji Confluence
- Użytkownik testowy utworzony w aplikacji serwera Confluence

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego aplikacji Confluence do testowania czynności opisanych w tym samouczku. Integrację należy najpierw przetestować w środowisku programistycznym lub przejściowym aplikacji, a dopiero później użyć środowiska produkcyjnego.

Aby rozpocząć, potrzebujesz następujących elementów:

* Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subluence SAML SSO przez microsoft single sign-on (Logowanie jednokrotne) włączone subskrypcji.

## <a name="supported-versions-of-confluence"></a>Obsługiwane wersje aplikacji Confluence

Aktualnie obsługiwane są następujące wersje aplikacji Confluence:

- Zbieg: 5,0 do 5,10
- Zbieg: 6.0.1 do 6.15.9
- Zbieg: 7.0.1 do 7.1.0

> [!NOTE]
> Należy pamiętać, że nasza wtyczka Confluence działa również na Ubuntu w wersji 16.04

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Program Confluence SAML SSO by Microsoft obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Dodawanie aplikacji Confluence SAML SSO by Microsoft z galerii

Aby skonfigurować integrację aplikacji Confluence SAML SSO by Microsoft z usługą Azure AD, musisz dodać aplikację Confluence SAML SSO by Microsoft z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania **wpisaj sytowy SSO SAML Confluence by Microsoft.**
1. Wybierz **confluence SAML SSO przez firmę Microsoft** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla logowania sytowego SAML zbiegu przez firmę Microsoft

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą sytuowania SSO SAML confluence przez firmę Microsoft przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Confluence SAML SSO firmy Microsoft.

Aby skonfigurować i przetestować syg jako jeden dla usługi Azure AD z przyśmieniu SSO SAML confluence przez firmę Microsoft, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj SSO SAML Confluence by Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz sytów SAML Confluence przez użytkownika testowego firmy Microsoft](#create-confluence-saml-sso-by-microsoft-test-user)** — aby mieć odpowiednik B.Simon in Confluence SAML SSO przez firmę Microsoft, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie **SSO saml zbiegu przez** aplikację firmy Microsoft znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<domain:port>/plugins/servlet/saml/auth`

    b. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<domain:port>/`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Port jest opcjonalny, jeśli adres URL zawiera nazwę. Te wartości można uzyskać podczas konfigurowania wtyczki Confluence, co jest objaśnione w dalszej części tego samouczka.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do confluence SAML SSO przez firmę Microsoft.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Confluence SAML SSO by Microsoft**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Konfigurowanie sytów SSO SAML zbiegu przez firmę Microsoft SSO

1. W innym oknie przeglądarki internetowej zaloguj się do instancji Confluence jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon1.png)

1. Pobierz wtyczkę z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Ręcznie przekaż wtyczkę udostępnioną przez firmę Microsoft za pomocą menu **Przekazywanie dodatku**. Pobieranie wtyczki jest objęte [umową serwisową firmy Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon12.png)

1. W przypadku uruchamiania scenariusza odwrotnego serwera proxy confluence lub modułu równoważenia obciążenia wykonaj następujące kroki:

    > [!NOTE]
    > Najpierw skonfiguruj serwer za pomocą poniższych instrukcji, a następnie zainstaluj wtyczkę.

    a. Dodaj poniższy atrybut w elemencie **Connector** port w pliku **server.xml** aplikacji serwera JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Zmień wartość w polu **Podstawowy adres URL** w obszarze **Ustawienia systemu** odpowiednio dla serwera proxy lub modułu równoważenia obciążenia.

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Po zainstalowaniu wtyczki jest ona wyświetlana w sekcji dodatków **Zainstalowane przez użytkownika** w ramach sekcji **Zarządzanie dodatkami**. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon15.png)

1. Wykonaj następujące kroki na stronie konfiguracji:

    ![Konfigurowanie logowania jednokrotnego](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Upewnij się, że tylko jeden certyfikat jest zamapowany do aplikacji, aby zapobiec błędom podczas rozpoznawania metadanych. Jeśli dostępnych jest wiele certyfikatów, administrator otrzyma błąd podczas rozpoznawania metadanych.

    1. W polu tekstowym **Adres URL metadanych** wklej wartość pola **Adres URL metadanych federacyjnych aplikacji** skopiowaną z witryny Azure Portal i kliknij przycisk **Rozpoznaj**. Spowoduje to odczytanie adresu URL metadanych dostawcy tożsamości i wypełnienie wszystkich pól danymi.

    1. Skopiuj wartości **identyfikatora, adresu URL odpowiedzi i adresu URL logowania**, a następnie wklej je odpowiednio w polach tekstowych **Identyfikator, Adres URL odpowiedzi i Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    1. W polu **Nazwa przycisku logowania** wpisz nazwę przycisku, która według Twojej organizacji ma być wyświetlana użytkownikom na ekranie logowania.
    
    1. W **opisie przycisku logowania** wpisz opis przycisku, który chcesz, aby użytkownicy zobaczyli go na ekranie logowania.

    1. W polu **Lokalizacje identyfikatora użytkownika SAML** wybierz opcję **Identyfikator użytkownika znajduje się w elemencie NameIdentifier instrukcji Subject** lub **Identyfikator użytkownika znajduje się w elemencie Attribute**.  Ten identyfikator musi być identyfikatorem użytkownika Confluence. Jeśli identyfikator użytkownika nie jest dopasowany, system nie zezwoli użytkownikom na logowanie. 

       > [!Note]
       > Domyślna lokalizacja identyfikatora użytkownika SAML to element NameIdentifier. Można ją zmienić za pomocą opcji elementu Attribute, podając nazwę odpowiedniego atrybutu.

    1. Jeśli wybierzesz nazwę użytkownika w opcji **elementu Atrybut,** w a następnie w **obszarze tekstowym Nazwa atrybutu** wpisz nazwę atrybutu, w którym oczekiwana jest nazwa użytkownika. 

    1. Jeśli używasz domeny federacyjnej (na przykład usług ADFS itp.) w połączeniu z usługą Azure AD, kliknij opcję **Włącz odnajdywanie obszaru głównego** i skonfiguruj pole **Nazwa domeny**.

    1. W polu **Nazwa domeny** wpisz nazwę domeny w przypadku logowania za pomocą usług ADFS.

    1. Zaznacz **opcję Włącz wylogowanie jednokrotne,** jeśli chcesz wylogować się z usługi Azure AD, gdy użytkownik wyloguje się z confluence. 

    1. Włącz pole wyboru **Force Azure Login,** jeśli chcesz zalogować się tylko za pomocą poświadczeń usługi Azure AD.

       > [!Note]
       > Aby włączyć domyślny formularz logowania do logowania administratora na stronie logowania po włączeniu funkcji force azure login, dodaj parametr zapytania w adresie URL przeglądarki.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

       > [!NOTE]
       > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów, odwiedź [przewodnik administracyjny ms confluence sso łącznika](../ms-confluence-jira-plugin-adminguide.md). Istnieje również [często](../ms-confluence-jira-plugin-faq.md) zadawane pytania dotyczące twojej pomocy.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Tworzenie użytkownika testowego aplikacji Confluence SAML SSO by Microsoft

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do serwera lokalnego Confluence, muszą one być aprowidywne do confluence SAML SSO przez firmę Microsoft. W przypadku aplikacji Confluence SAML SSO by Microsoft aprowizowanie to zadanie wykonywane ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego Confluence jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Dodawanie pracownika](./media/confluencemicrosoft-tutorial/user1.png)

1. W sekcji Użytkownicy kliknij pozycję **Dodaj użytkowników.** Na stronie okna **dialogowego Dodawanie użytkownika** wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/confluencemicrosoft-tutorial/user2.png)

    a. W polu **tekstowym Nazwa użytkownika** wpisz adres e-mail użytkownika takiego jak B.Simon.

    b. W polu **tekstowym Imię i nazwisko** wpisz pełną nazwę użytkownika, takiego jak B.Simon.

    d. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. B.Simon@contoso.com.

    d. W **polach tekstowych Hasło** wpisz hasło do B.Simon.

    e. Kliknij pozycję **Confirm Password** (Potwierdź hasło), a następnie ponownie wprowadź hasło.

    f. Kliknij przycisk **Add** (Dodaj).

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Confluence SAML SSO by Microsoft na panelu dostępu powinno nastąpić automatyczne zalogowanie do programu Confluence SAML SSO by Microsoft, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj sytuacyję SSO SAML firmy Microsoft z usługą Azure AD](https://aad.portal.azure.com/)
