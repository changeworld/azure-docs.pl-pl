---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z elementami SuccessFactors | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniem SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49915271580b5665981bf7e212f3d5712c86456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76292983"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z czynnikami successfactor

W tym samouczku dowiesz się, jak zintegrować SuccessFactors z usługą Azure Active Directory (Azure AD). Po zintegrowaniu SuccessFactors z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do SuccessFactors.
* Włącz użytkownikom automatyczne logowanie do SuccessFactors za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej SuccessFactors (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* SuccessFactors **obsługuje** sp zainicjowane SSO.
* Po skonfigurowaniu SuccessFactors można wymusić formanty sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>Dodawanie rozwiązania SuccessFactors z galerii

Aby skonfigurować integrację rozwiązania SuccessFactors z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SuccessFactors** w polu wyszukiwania.
1. Wybierz **SuccessFactors** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Konfigurowanie i testowanie usługi Azure AD SSO pod kątem czynników sukcesu

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą successfactors przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby identyfikator YSO działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w successfactors.

Aby skonfigurować i przetestować sycowe narzędzie platformy Azure AD z elementami successfactors, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj logowanie jednokrotne SuccessFactors](#configure-successfactors-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SuccessFactors](#create-successfactors-test-user)** — aby mieć odpowiednik B.Simon w SuccessFactors, który jest połączony z reprezentacją użytkownika usługi Azure AD.
3. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **SuccessFactors** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

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

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

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

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie successfactors** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do SuccessFactors.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SuccessFactors**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-successfactors-sso"></a>Konfigurowanie sytcha liczby generów SuccessFactors

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

    d. W polu tekstowym **Issuer URL** (Adres URL wystawcy) wklej wartość pola **Identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

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

    d. W polu **Require sp must encrypt all NameID element** (Wymagaj szyfrowania wszystkich elementów NameID przez dostawcę usług) wybierz wartość **No** (Nie).

    d. W polu **NameID Format** (Format identyfikatora NameID) wybierz pozycję **unspecified** (nieokreślony).

    e. W polu **Enable sp initiated login (AuthnRequest)** (Włącz logowanie inicjowane przez dostawcę usług: AuthnRequest) wybierz pozycję **Yes** (Tak).

    f. W polu tekstowym **Send request as Company-Wide issuer** (Wyślij żądanie jako wystawca firmowy) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

6. Wykonaj następujące kroki, aby w nazwach logowania użytkowników nie była uwzględniania wielkość liter.

    ![Konfigurowanie logowania jednokrotnego][29]

    a. Przejdź na stronę **Company Settings** (Ustawienia firmowe) (w dolnej części).

    b. Zaznacz pole wyboru obok pozycji **Enable Non-Case-Sensitive Username** (Zezwól na nazwę użytkownika bez uwzględnienia wielkości liter).

    d. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Jeśli spróbujesz włączyć tę funkcję, system sprawdzi, czy utworzona nie zostanie zduplikowana nazwa logowania protokołu SAML. Może to mieć miejsce, jeśli klient ma użytkowników o nazwach Użytkownik1 i użytkownik1. Wyłączenie uwzględniania wielkości liter spowoduje występowanie takich duplikatów. W systemie zostanie wyświetlony komunikat i ta funkcja nie zostanie włączona. Klient musi zmienić jedną z nazw użytkowników na inną.

### <a name="create-successfactors-test-user"></a>Tworzenie użytkownika testowego rozwiązania SuccessFactors

Aby umożliwić użytkownikom usługi Azure AD zalogować się do SuccessFactors, muszą one być aprowizowana do SuccessFactors. W przypadku rozwiązania SuccessFactors aprowizacja to zadanie ręczne.

Aby utworzyć użytkownik w rozwiązaniu SuccessFactors, skontaktuj się z [zespołem pomocy technicznej tego rozwiązania](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SuccessFactors w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania SuccessFactors, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj czynniki successfactors za pomocą usługi Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić SuccessFactors dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
