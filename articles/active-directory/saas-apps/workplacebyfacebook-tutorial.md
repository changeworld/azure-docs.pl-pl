---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z Workplace przez Facebooka | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de84f2aee5f59d14ab70cb1687968643c4cdb31e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136384"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z Workplace przez Facebooka

W tym samouczku dowiesz się, jak zintegrować workplace przez Facebook z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Workplace by Facebook z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Workplace przez Facebook.
* Włącz automatyczne logowanie użytkowników do Workplace przez Facebooka za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego workplace by Facebook .

> [!NOTE]
> Serwis Facebook oferuje dwa produkty: Workplace Standard (bezpłatny) i Workplace Premium (płatny). W przypadku dowolnej dzierżawy Workplace Premium można skonfigurować integrację ze standardem SCIM i logowaniem jednokrotnym bez żadnych dodatkowych kosztów czy licencji. Logowanie jednokrotne i standard SCIM nie są dostępne w wystąpieniach Workplace Standard.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja Workplace by Facebook obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**
* Aplikacja Workplace by Facebook obsługuje **aprowizację just in time**
* Aplikacja Workplace by Facebook obsługuje **[automatyczne aprowizowanie użytkowników](workplacebyfacebook-provisioning-tutorial.md)**
* Aplikacja Workplace by Facebook Mobile można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia usługi SSO. W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.
* Po skonfigurowaniu Workplace by Facebook możesz wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Dodawanie aplikacji Workplace by Facebook z galerii

Aby skonfigurować integrację aplikacji Workplace by Facebook z usługą Azure AD, należy dodać aplikację Workplace by Facebook z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Workplace by Facebook** w polu wyszukiwania.
1. Wybierz **pozycję Workplace by Facebook** w panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Konfigurowanie i testowanie usługi Azure AD SSO dla workplace przez Facebook

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu workplace przez Facebooka przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Workplace by Facebook.

Aby skonfigurować i przetestować usługę Azure AD SSO przy pracy przez Facebooka, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj workplace przez facebook logowanie —](#configure-workplace-by-facebook-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz workplace przez użytkownika testowego Facebook](#create-workplace-by-facebook-test-user)** - mieć odpowiednik B.Simon w miejscu pracy przez Facebook, który jest połączony z reprezentacji usługi Azure AD użytkownika.
3. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie Integracja aplikacji **Workplace by Facebook** znajdź sekcję **Zarządzaj** i wybierz **pozycję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instancename>.facebook.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://www.facebook.com/company/<instanceID>`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Zobacz stronę uwierzytelniania na pulpicie nawigacyjnym firmy workplace, aby uzyskać poprawne wartości dla społeczności Workplace, co zostało wyjaśnione w dalszej części samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie miejsca pracy przez Facebooka** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Workplace przez Facebook.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję Workplace by Facebook**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-workplace-by-facebook-sso"></a>Konfigurowanie workplace przez Facebook SSO

1. Aby zautomatyzować konfigurację w Workplace by Facebook, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Przycisk Skonfiguruj Workplace przez Facebooka,** przekieruje Cię do aplikacji Workplace by Facebook. W tym miejscu podaj poświadczenia administratora, aby zalogować się do Workplace przez Facebooka. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-5.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować Workplace by Facebook, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Workplace by Facebook jako administrator i wykonaj następujące czynności:

    > [!NOTE]
    > W ramach procesu uwierzytelniania SAML aplikacja Workplace może używać ciągów zapytań o rozmiarze do 2,5 kilobajta w celu przekazania parametrów do usługi Azure AD.

1. Na lewym panelu nawigacyjnym przejdź do karty**Uwierzytelnianie** **zabezpieczeń.** > 

    ![Panel administracyjny](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Sprawdź opcję **Logowanie jednokrotne włączone(logowanie jednokrotne).**
    
    b. Kliknij **+Dodaj nowego dostawcę jednopisu**.
    > [!NOTE]
    > Upewnij się, że zaznaczysz również pole wyboru Logowanie hasłem. Administratorzy mogą potrzebować tej opcji do logowania podczas przerzucenia certyfikatu, aby zatrzymać się zablokowane.

1. Na karcie **Uwierzytelnianie** wybierz pozycję **Logowanie jednokrotne**, a następnie wykonaj następujące czynności:

    ![Karta Uwierzytelnianie](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. W **nazwie dostawcy usługi SSO**wprowadź nazwę wystąpienia samego dostawcy, taką jak Azureadsso.

    b. W polu tekstowym **Adres URL SAML** wklej wartość **Adres URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **adresu URL wystawcy SAML** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    d. Otwórz w Notatniku swój **certyfikat zakodowany w formacie base-64** pobrany z witryny Azure Portal, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat SAML**.

    e. Skopiuj **adres URL grupy odbiorców** dla swojego wystąpienia i wklej go w skrzynce tekstowej Identyfikator **(identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    f. Skopiuj **adres URL adresata** dla swojego wystąpienia i wklej go w obszarze tekstowym **Zaloguj się na adres URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    g. Skopiuj **adres URL usługi ACS (Asercja Konsument Service)** dla swojego wystąpienia i wklej go w skrzynce tekstowej **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    h. Przewiń w dół do końca sekcji i kliknij przycisk **Testuj logowanie jednokrotne**. Zostanie wtedy otworzone okno podręczne ze stroną logowania usługi Azure AD. Wprowadź swoje poświadczenia jak zwykle w celu uwierzytelnienia.

    **Rozwiązywanie problemów:** Upewnij się, że adres e-mail zwracany z usługi Azure AD jest taki sam, jak konto Workplace, na które użytkownik jest zalogowany.

    i. Po pomyślnym zakończeniu testu przewiń w dół do końca strony i kliknij przycisk **Zapisz**.

    j. Wszystkim użytkownikom korzystającym z aplikacji Workplace zostanie wyświetlona strona logowania usługi Azure AD w celu uwierzytelnienia.

1. **Przekierowanie wylogowania SAML (opcjonalnie)** -

    Możesz opcjonalnie skonfigurować adres URL wylogowywania SAML, który może prowadzić do strony wylogowywania usługi Azure AD. Gdy to ustawienie jest włączone i skonfigurowane, użytkownik nie będzie przekierowywany do strony wylogowywania aplikacji Workplace. Zamiast tego użytkownik zostanie przekierowany do adresu URL dodanego do ustawienia Przekierowanie po wylogowaniu SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfigurowanie częstotliwości ponownego uwierzytelniania

Możesz skonfigurować aplikację Workplace, aby wyświetlała monit o sprawdzenie protokołu SAML codziennie, co tydzień, co 2 tygodnie, co miesiąc lub aby nie wyświetlała go wcale.

> [!NOTE]
> Minimalna wartość sprawdzania protokołu SAML w przypadku aplikacji mobilnych to 1 tydzień.

Można również wymusić reset SAML dla wszystkich użytkowników za pomocą przycisku: Wymagaj uwierzytelniania SAML dla wszystkich użytkowników teraz.

### <a name="create-workplace-by-facebook-test-user"></a>Tworzenie użytkownika testowego aplikacji Workplace by Facebook

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Workplace przez Facebooka. Aplikacja Workplace by Facebook obsługuje aprowizację just in time, która jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje w aplikacji Workplace by Facebook, podczas próby uzyskania dostępu do aplikacji Workplace by Facebook zostanie utworzony nowy użytkownik.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Workplace by Facebook](https://workplace.fb.com/faq/)

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Workplace by Facebook w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Workplace by Facebook, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Test SSO dla miejsca pracy przez Facebook (telefon komórkowy)

1. Otwórz workplace przez aplikację Facebook Mobile. Na stronie logowania kliknij pozycję **ZALOGUJ się**.

    ![Logowanie](./media/workplacebyfacebook-tutorial/test05.png)

2. Wprowadź firmową poczytę poczty e-mail i kliknij przycisk **KONTYNUUJ**.

    ![Wiadomość e-mail](./media/workplacebyfacebook-tutorial/test02.png)

3. Kliknij **tylko raz**.

    ![Raz](./media/workplacebyfacebook-tutorial/test04.png)

4. Kliknij pozycję **Zezwalaj**.

    ![Zezwalaj na](./media/workplacebyfacebook-tutorial/test03.png)

5. Wreszcie po pomyślnym zalogowaniu zostanie wyświetlona strona główna aplikacji.    

    ![Strona główna](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie inicjowania obsługi administracyjnej użytkowników](workplacebyfacebook-provisioning-tutorial.md)

- [Wypróbuj Workplace by Facebook dzięki usłudze Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
