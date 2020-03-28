---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z kontrolerem Check Point CloudGuard Dome9 Arc | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a kontrolerem CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73885348"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z pakietem Check Point CloudGuard Dome9 Arc

W tym samouczku dowiesz się, jak zintegrować check point CloudGuard Dome9 Arc z usługą Azure Active Directory (Azure AD). Po zintegrowaniu check point CloudGuard Dome9 Arc z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do check point CloudGuard Dome9 Arc.
* Włącz automatyczne logowanie użytkowników do check point CloudGuard Dome9 Arc za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej Check Point CloudGuard Dome9 Arc .Check Point CloudGuard Dome9 Arc single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Check Point CloudGuard Dome9 Arc obsługuje jednostki SSO inicjowane przez **SP i IDP**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Dodawanie check point CloudGuard Dome9 Arc z galerii

Aby skonfigurować integrację systemu Check Point CloudGuard Dome9 Arc z usługą Azure AD, należy dodać check point CloudGuard Dome9 Arc z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania wpisz **polecenie Check Point CloudGuard Dome9 Arc.**
1. Wybierz **polecenie Check Point CloudGuard Dome9 Arc** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla systemu Check Point CloudGuard Dome9 Arc

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą funkcji Check Point CloudGuard Dome9 Arc przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w systemie Check Point CloudGuard Dome9 Arc.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą systemu Check Point CloudGuard Dome9 Arc, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie SSO systemu Check Point CloudGuard Dome9 Arc](#configure-check-point-cloudguard-dome9-arc-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz check point CloudGuard Dome9 Arc test użytkownika](#create-check-point-cloudguard-dome9-arc-test-user)** — mieć odpowiednik B.Simon w Check Point CloudGuard Dome9 Arc, który jest połączony z reprezentacji azure ad użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Check Point CloudGuard Dome9 Arc** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL:`https://secure.dome9.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. `<company name>` Wartość otrzymasz z sekcji **Configure Check Point CloudGuard Dome9 Arc SSO,** która została wyjaśniona w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Check Point CloudGuard Dome9 Arc oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższego, Check Point CloudGuard Dome9 Arc aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Kliknij [tutaj,](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) aby dowiedzieć się, jak utworzyć role w usłudze Azure AD.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Set up Check Point CloudGuard Dome9 Arc** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Check Point CloudGuard Dome9 Arc.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **opcję Check Point CloudGuard Dome9 Arc**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Konfigurowanie sytów SSO cloudguard dome9 w punkcie kontrolnym

1. Aby zautomatyzować konfigurację w obrębie check point CloudGuard Dome9 Arc, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Setup Check Point CloudGuard Dome9 Arc** przekieruje Cię do aplikacji Check Point CloudGuard Dome9 Arc. Stamtąd podaj poświadczenia administratora, aby zalogować się do Check Point CloudGuard Dome9 Arc. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować check point CloudGuard Dome9 Arc, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Check Point CloudGuard Dome9 Arc jako administrator i wykonaj następujące czynności:

2. Kliknij pozycję **Profile Settings** (Ustawienia profilu) w prawym górnym rogu, a następnie kliknij przycisk **Account Settings** (Ustawienia konta). 

    ![Konfiguracja łuku Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure1.png)

3. Przejdź na kartę **SSO** (Logowanie jednokrotne), a następnie kliknij przycisk **ENABLE** (WŁĄCZ).

    ![Konfiguracja łuku Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure2.png)

4. W sekcji SSO Configuration (Konfiguracja logowania jednokrotnego) wykonaj następujące czynności:

    ![Konfiguracja łuku Check Point CloudGuard Dome9](./media/dome9arc-tutorial/configure3.png)

    a. Wprowadź nazwę firmy w polu tekstowym **Account ID** (Identyfikator konta). Ta wartość ma być używana w **adresie** URL odpowiedzi i **logowania,** o którym mowa w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. W polu tekstowym **wystawcy** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany w witrynie Azure portal.

    d. W polu tekstowym **Idp endpoint url** (Adres URL punktu końcowego dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. Otwórz w Notatniku pobrany certyfikat zakodowany w formacie Base64, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **SAML X.509 certificate** (Certyfikat X.509 SAML).

    e. Kliknij przycisk **Zapisz**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Utwórz użytkownika testowego Check Point CloudGuard Dome9 Arc

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do check point CloudGuard Dome9 Arc, muszą być aprowizowani do aplikacji. Check Point CloudGuard Dome9 Arc obsługuje inicjowanie obsługi administracyjnej just-in-time, ale aby działał poprawnie, użytkownik musi wybrać **określoną rolę** i przypisać to samo do użytkownika.

   >[!Note]
   >Aby uzyskać informacje o tworzeniu **ról** i innych szczegółach, skontaktuj się z [zespołem pomocy technicznej klienta systemu Check Point CloudGuard Dome9 Arc.](mailto:Dome9@checkpoint.com)

**Aby ręcznie aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Check Point CloudGuard Dome9 Arc jako administrator.

2. Kliknij pozycję **Users & Roles** (Użytkownicy i role), a następnie kliknij pozycję **Users** (Użytkownicy).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user1.png)

3. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user2.png)

4. W sekcji **Create User** (Tworzenie użytkownika) wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user3.png)

    a. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład B.Simon@contoso.com.

    b. W polu tekstowym **Imię** wpisz imię użytkownika, takiego jak B.

    d. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    d. W polu **SSO User** (Użytkownik logowania jednokrotnego) wybierz ustawienie **On** (Włączone).

    e. Kliknij **przycisk UTWÓRZ**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Check Point CloudGuard Dome9 Arc w Panelu dostępu należy automatycznie zalogować się do systemu Check Point CloudGuard Dome9 Arc, dla którego skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj check point CloudGuard Dome9 Arc z usługą Azure AD](https://aad.portal.azure.com/)