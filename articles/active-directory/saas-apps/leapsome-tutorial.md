---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z Leapsome | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28b95e779e2b814b0ae91059c3edd12644d7c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430939"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z leapsome

W tym samouczku dowiesz się, jak zintegrować Leapsome z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Leapsome z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Leapsome.
* Włącz użytkownikom automatycznie zalogowany do Leapsome z ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Leapsome rejestracji jednokrotnej (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Leapsome obsługuje **SP i IDP** zainicjowane SSO

## <a name="adding-leapsome-from-the-gallery"></a>Dodawanie Leapsome z galerii

Aby skonfigurować integrację Leapsome do usługi Azure AD, należy dodać Leapsome z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Leapsome** w polu wyszukiwania.
1. Wybierz **opcję Leapsome** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla leapsome

Konfigurowanie i testowanie usługi Azure AD SSO z Leapsome przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Leapsome.

Aby skonfigurować i przetestować usługę Azure AD SSO przy pomocą leapsome, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj leapsome SSO](#configure-leapsome-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz leapsome użytkownika testowego](#create-leapsome-test-user)** — mieć odpowiednik B.Simon w Leapsome, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Leapsome** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL:`https://www.leapsome.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > Poprzedni adres URL odpowiedzi i wartość adresu URL logowania nie jest wartością rzeczywistą. Zaktualizujesz je za pomocą rzeczywistych wartości, co wyjaśniono w dalszej części samouczka.

1. Leapsome aplikacji oczekuje potwierdzeń SAML w określonym formacie, który wymaga, aby dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji Leapsome oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy | Przestrzeń nazw |
    | ---------------| --------------- | --------- |  
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | obraz | Adres URL do zdjęcia pracownika | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > Wartość atrybutu obrazu nie jest rzeczywista. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL obrazu. Aby uzyskać tę wartość skontaktuj się z [zespołem pomocy technicznej Leapsome Client](mailto:support@leapsome.com).

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie leapsome** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Leapsome.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz opcję **Leapsome**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-leapsome-sso"></a>Konfigurowanie leapsome SSO

1. W innym oknie przeglądarki internetowej zaloguj się do Leapsome jako administrator zabezpieczeń.

1. W prawym górnym rogu kliknij logo Ustawienia, a następnie kliknij pozycję **Ustawienia administratora**.

    ![Skaczący zestaw](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Na pasku menu po lewej stronie kliknij przycisk **Logowanie jednokrotne (SSO),** a na stronie **logowania jednokrotnego opartego na SAML (SSO)** wykonaj następujące czynności:

    ![Skaczący saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Wybierz **włącz logowanie jednookiewanie oparte na SAML**.

    b. Skopiuj wartość **adresu URL logowania (wskaż użytkownikom tutaj, aby rozpocząć logowanie)** i wklej ją do pola **tekstowego Adresu URL logowania** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    d. Skopiuj wartość **adresu URL odpowiedzi (odbiera odpowiedź od dostawcy tożsamości)** i wklej ją do pola tekstowego **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    d. W polu tekstowym **logowania do logowania logowania logowania (dostarczone przez dostawcę tożsamości)** wklej wartość adresu URL **logowania,** który został skopiowany z witryny Azure portal.

    e. Skopiuj certyfikat pobrany z `--BEGIN CERTIFICATE and END CERTIFICATE--` witryny Azure portal bez komentarzy i wklej go w skrzynce tekstowej **Certyfikat (dostarczony przez dostawcę tożsamości).**

    f. Kliknij **pozycję AKTUALIZUJ USTAWIENIA logowania jedno i przesuń**.

### <a name="create-leapsome-test-user"></a>Utwórz leapsome użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Leapsome. Praca z [zespołem pomocy technicznej Leapsome Client,](mailto:support@leapsome.com) aby dodać użytkowników lub domeny, które muszą zostać dodane do listy dozwolonych dla platformy Leapsome. Jeśli domena zostanie dodana przez zespół, użytkownicy zostaną automatycznie aprowizowani do platformy Leapsome. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Leapsome w Panelu dostępu należy automatycznie zalogować się do leapsome, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Spróbuj Leapsome z usługą Azure AD](https://aad.portal.azure.com/)