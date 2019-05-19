---
title: 'Samouczek: integracja usługi Azure Active Directory z rozwiązaniem SuccessFactors | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniem SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2281fb046ca9b96aa5800150d630b9086236c5c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65866662"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem SuccessFactors

Z tego samouczka dowiesz się, jak zintegrować rozwiązanie SuccessFactors z usługą Azure Active Directory (Azure AD).
Zintegrowanie rozwiązania SuccessFactors z usługą Azure AD zapewnia następujące korzyści:

* Możliwość kontrolowania dostępu do rozwiązania SuccessFactors za pomocą usługi Azure AD.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do rozwiązania SuccessFactors (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z rozwiązaniem SuccessFactors potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja rozwiązania SuccessFactors z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie SuccessFactors obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-successfactors-from-the-gallery"></a>Dodawanie rozwiązania SuccessFactors z galerii

Aby skonfigurować integrację rozwiązania SuccessFactors z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązanie SuccessFactors z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SuccessFactors**, wybierz pozycję **SuccessFactors** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Pozycja SuccessFactors na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z rozwiązaniem SuccessFactors, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania SuccessFactors.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z rozwiązaniem SuccessFactors, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w rozwiązaniu SuccessFactors](#configure-successfactors-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego rozwiązania SuccessFactors](#create-successfactors-test-user)** — aby w rozwiązaniu SuccessFactors istniał odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z rozwiązaniem SuccessFactors, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **SuccessFactors** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL rozwiązania SuccessFactors na potrzeby logowania jednokrotnego](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html), aby uzyskać te wartości.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie rozwiązania SuccessFactors** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-successfactors-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w rozwiązaniu SuccessFactors

1. W innym oknie przeglądarki internetowej zaloguj się do **portalu administracyjnego rozwiązania SuccessFactors** jako administrator.

2. Przejdź na stronę **Application Security** (Zabezpieczenia aplikacji) zawierającą pole **Single Sign On Features** (Funkcje logowania jednokrotnego).

3. Umieść dowolną wartość w polu **Reset Token** (Token resetowania), a następnie kliknij przycisk **Save Token** (Zapisz token), aby włączyć logowanie jednokrotne protokołu SAML.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji][11]

    > [!NOTE]
    > Ta wartość jest używana jako przełącznik włączania/wyłączania. Jeśli zostanie zapisana jakakolwiek wartość, logowanie jednokrotne protokołu SAML będzie włączone. Jeśli zostanie zapisana pusta wartość, logowanie jednokrotnego protokołu SAML będzie wyłączone.

4. Zapoznaj się z poniższym zrzutem ekranu i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji][12]
  
    a. Zaznacz przycisk radiowy **SAML v2 SSO** (Logowanie jednokrotne protokołu SAML w wersji 2)
  
    b. Ustaw wartość pola **SAML Asserting Party Name** (Nazwa jednostki potwierdzającej protokołu SAML) na przykład na wystawcę protokołu SAML i nazwę firmy.

    c. W polu tekstowym **Issuer URL** (Adres URL wystawcy) wklej wartość pola **Identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. W polu **Require Mandatory Signature** (Wymagaj obowiązkowego podpisu) wybierz wartość **Assertion** (Asercja).

    e. W polu **Enable SAML Flag** wybierz wartość **Enabled** (Włączone).

    f. W polu **Login Request Signature(SF Generated/SP/RP)** (Sygnatura żądania logowania: wygenerowane w rozwiązaniu SF/Dostawca usług/Dostawca zasobów) wybierz wartość **Nie**.

    g. W polu **SAML Profile** (Profil protokołu SAML) wybierz wartość **Browser/Post Profile** (Przeglądarka/Opublikuj profil).

    h. W polu **Enforce Certificate Valid Period** (Wymuś prawidłowy okres certyfikatu) wybierz wartość **No** (Nie).

    i. Skopiuj zawartość pliku certyfikatu pobranego z witryny Azure Portal, a następnie wklej go do pola tekstowego **SAML Verifying Certificate** (Certyfikatu weryfikacji protokołu SAML).

    > [!NOTE] 
    > Zawartość certyfikatu musi rozpoczynać się od tagów początku i końca certyfikatu.

5. Przejdź do strony protokołu SAML w wersji 2, a następnie wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji][13]

    a. W polu **Support SP-initiated Global Logout** (Obsługuj globalne wylogowywanie inicjowane przez dostawcę usług) wybierz pozycję **Yes** (Tak).

    b. W polu tekstowym **Global Logout Service URL (LogoutRequest destination)** (Adres URL usługi globalnego wylogowywania: miejsce docelowe elementu LogoutRequest) wklej wartość pola **Adres URL wylogowywania** skopiowaną z witryny Azure Portal.

    c. W polu **Require sp must encrypt all NameID element** (Wymagaj szyfrowania wszystkich elementów NameID przez dostawcę usług) wybierz wartość **No** (Nie).

    d. W polu **NameID Format** (Format identyfikatora NameID) wybierz pozycję **unspecified** (nieokreślony).

    e. W polu **Enable sp initiated login (AuthnRequest)** (Włącz logowanie inicjowane przez dostawcę usług: AuthnRequest) wybierz pozycję **Yes** (Tak).

    f. W polu tekstowym **Send request as Company-Wide issuer** (Wyślij żądanie jako wystawca firmowy) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

6. Wykonaj następujące kroki, aby w nazwach logowania użytkowników nie była uwzględniania wielkość liter.

    ![Konfigurowanie logowania jednokrotnego][29]

    a. Przejdź na stronę **Company Settings** (Ustawienia firmowe) (w dolnej części).

    b. Zaznacz pole wyboru obok pozycji **Enable Non-Case-Sensitive Username** (Zezwól na nazwę użytkownika bez uwzględnienia wielkości liter).

    c. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Jeśli spróbujesz włączyć tę funkcję, system sprawdzi, czy utworzona nie zostanie zduplikowana nazwa logowania protokołu SAML. Może to mieć miejsce, jeśli klient ma użytkowników o nazwach Użytkownik1 i użytkownik1. Wyłączenie uwzględniania wielkości liter spowoduje występowanie takich duplikatów. W systemie zostanie wyświetlony komunikat i ta funkcja nie zostanie włączona. Klient musi zmienić jedną z nazw użytkowników na inną.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do rozwiązania SuccessFactors.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **SuccessFactors**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **SuccessFactors**.

    ![Link do rozwiązania SuccessFactors na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-successfactors-test-user"></a>Tworzenie użytkownika testowego rozwiązania SuccessFactors

Aby użytkownicy usługi Azure AD mogli logować się do rozwiązania SuccessFactors, muszą oni zostać zaaprowizowani w tym rozwiązaniu. W przypadku rozwiązania SuccessFactors aprowizacja to zadanie ręczne.

Aby utworzyć użytkownik w rozwiązaniu SuccessFactors, skontaktuj się z [zespołem pomocy technicznej tego rozwiązania](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SuccessFactors w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania SuccessFactors, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
