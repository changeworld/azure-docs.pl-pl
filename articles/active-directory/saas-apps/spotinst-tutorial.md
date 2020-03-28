---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z spotinst | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5490ff6c6143dff258d74e013bb9d4c821aab625
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263289"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z spotinst

W tym samouczku dowiesz się, jak zintegrować Spotinst z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Spotinst z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Spotinst.
* Włącz użytkownikom automatyczne logowanie do Spotinst za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją rejestracji jednokrotnej spotinst.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Spotinst obsługuje jednostki SSO inicjowane przez **SP i IDP**

## <a name="adding-spotinst-from-the-gallery"></a>Dodawanie Spotinst z galerii

Aby skonfigurować integrację Spotinst z usługą Azure AD, należy dodać Spotinst z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Spotinst** w polu wyszukiwania.
1. Wybierz **Spotinst** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla spotinst

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą spotinst przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Spotinst.

Aby skonfigurować i przetestować sylog usługi Azure AD za pomocą spotinst, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj spotinst SSO](#configure-spotinst-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Spotinst](#create-spotinst-test-user)** — aby mieć odpowiednik B.Simon w Spotinst, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Spotinst** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego.**
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    a. Sprawdź **ustaw dodatkowe adresy URL**.

    b. W polu tekstowym **Stan przekazywania** wpisz wartość:`<ID>`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > Wartość stanu przekazywania nie jest rzeczywista. Wartość stanu przekazywania zostanie zaktualizowana z rzeczywistą wartością stanu przekazywania, która została wyjaśniona w dalszej części samouczka.

1. Kliknij przycisk **Zapisz**.

1. Spotinst aplikacji oczekuje potwierdzeń SAML w określonym formacie, który wymaga, aby dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji Spotinst oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | -----| --------------- |
    | Adres e-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Spotinst** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Spotinst.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **spotinst**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-spotinst-sso"></a>Konfigurowanie spotinstu jako sytuaj

1. W innym oknie przeglądarki internetowej zaloguj się do Spotinst jako administrator zabezpieczeń.

2. Kliknij **ikonę użytkownika** w prawym górnym rogu ekranu i kliknij pozycję **Ustawienia**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Kliknij kartę **BEZPIECZEŃSTWO** u góry, a następnie wybierz pozycję **Dostawcy tożsamości** i wykonaj następujące czynności:

    ![Bezpieczeństwo spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Skopiuj wartość **stanu przekazywania** dla wystąpienia i wklej ją w polu **tekstowym stanu przekazywania** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. Kliknij **przycisk PRZEGLĄDAJ,** aby przekazać pobrany z witryny Azure portal plik xml metadanych

    d. Kliknij **przycisk ZAPISZ**.

### <a name="create-spotinst-test-user"></a>Utwórz użytkownika testowego Spotinst

Celem tej sekcji jest stworzenie użytkownika o nazwie Britta Simon w Spotinst.

1. Jeśli aplikacja została skonfigurowana w trybie inicjowanym w **uł.o.** wykonaj następujące czynności:

   a. W innym oknie przeglądarki internetowej zaloguj się do Spotinst jako administrator zabezpieczeń.

   b. Kliknij **ikonę użytkownika** w prawym górnym rogu ekranu i kliknij pozycję **Ustawienia**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    d. Kliknij **pozycję Użytkownicy** i wybierz pozycję **DODAJ UŻYTKOWNIKA**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. W sekcji dodaj użytkownika wykonaj następujące czynności:

    ![Ustawienia Spotinst](./media/spotinst-tutorial/adduser2.png)

    * W polu **tekstowym Imię i nazwisko** wprowadź pełną nazwę użytkownika, takiego jak **BrittaSimon**.

    * W polu tekstowym **Wiadomość e-mail** wprowadź `brittasimon\@contoso.com`adres e-mail użytkownika w stylu .

    * Wybierz szczegóły dotyczące organizacji dla **roli organizacji, roli konta i kont**.

2. Jeśli aplikacja została skonfigurowana w trybie inicjowanym **przez IDP,** w tej sekcji nie ma dla Ciebie żadnego elementu akcji. Spotinst obsługuje just-in-time inicjowania obsługi administracyjnej, która jest domyślnie włączona. Nowy użytkownik jest tworzony podczas próby uzyskania dostępu do Spotinst, jeśli jeszcze nie istnieje.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Spotinst w Panelu dostępu należy automatycznie zalogować się do Spotinst, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj spotinst za pomocą usługi Azure AD](https://aad.portal.azure.com/)

