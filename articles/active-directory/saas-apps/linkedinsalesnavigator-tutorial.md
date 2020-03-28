---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą LinkedIn Sales Navigator | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a nawigatorem sprzedaży LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d8293b23559860e70191576db13c3cd14f520e6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430882"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą LinkedIn Sales Navigator

W tym samouczku dowiesz się, jak zintegrować nawigatora sprzedaży LinkedIn z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Nawigatora Sprzedaży LinkedIn z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do LinkedIn Sales Navigator.
* Włącz automatyczne logowanie użytkowników do usługi LinkedIn Sales Navigator za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) usługi LinkedIn Sales Navigator.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* LinkedIn Sales Navigator obsługuje jednostkę SSO inicjowane przez sp **i idp**
* LinkedIn Sales Navigator obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time
* LinkedIn Sales Navigator obsługuje [ **automatyczne** inicjowanie obsługi administracyjnej użytkowników](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Dodawanie nawigatora sprzedaży LinkedIn z galerii

Aby skonfigurować integrację nawigatora sprzedaży linkedin z usługą Azure AD, należy dodać Nawigator sprzedaży LinkedIn z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **LinkedIn Sales Navigator** w polu wyszukiwania.
1. Wybierz **LinkedIn Sales Navigator** z panelu wyniki, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla nawigatora sprzedaży linkedin

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą linkedin sales navigator przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w nawigatorze sprzedaży linkedin.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD sytuować za pomocą linkedin sales navigator, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie logowania do sprzedaży LinkedIn Sales Navigator](#configure-linkedin-sales-navigator-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)** — aby mieć odpowiednik B.Simon w LinkedIn Sales Navigator, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Nawigatora Sprzedaży LinkedIn** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wprowadź wartość **identyfikatora jednostki,** a wartość identyfikatora jednostki zostanie skopiowana z portalu Linkedin wyjaśnionej w dalszej części tego samouczka.

    b. W polu tekstowym **Adres URL odpowiedzi** wprowadź wartość url dostępu do **konsumenta oświadczeń (ACS)** — skopiujesz wartość adresu URL dostępu do konsumenta oświadczeń z portalu Linkedin wyjaśnionej w dalszej części tego samouczka.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Aplikacja LinkedIn Sales Navigator oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji LinkedIn Sales Navigator oczekuje kilku więcej atrybutów, które zostaną przekazane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | --- | --- |
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    | Unikatowy identyfikator użytkownika | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie nawigatora sprzedaży linkedin** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do LinkedIn Sales Navigator.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **LinkedIn Sales Navigator**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-linkedin-sales-navigator-sso"></a>Konfigurowanie identyfikatora SYC nawigatora sprzedaży LinkedIn

1. W innym oknie przeglądarki internetowej zaloguj się do witryny **LinkedIn Sales Navigator** jako administrator.

1. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Ponadto wybierz pozycję **Nawigator sprzedaży** z listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij **lub kliknij tutaj, aby załadować i skopiować poszczególne pola z formularza** i wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Skopiuj **identyfikator jednostki** i wklej go do pola tekstowego **Identyfikator** w **podstawowej konfiguracji SAML** w witrynie Azure portal.

    b. Skopiuj **adres URL dostępu konsumenta do potwierdzenia (ACS)** i wklej go do pola tekstowego **Adres URL odpowiedzi** w **podstawowej konfiguracji SAML** w witrynie Azure portal.

1. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż plik XML pobrany z witryny Azure portal, klikając opcję **Przekaż plik XML.**

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan funkcji logowania jednokrotnego zmieni się z **Not Connected** (Niepołączona) na **Connected** (Połączona)

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Utwórz użytkownika testowego Nawigatora Sprzedaży LinkedIn

Aplikacja Linked Sales Navigator obsługuje inicjowanie obsługi administracyjnej użytkowników just in time (JIT), a po uwierzytelnianiu użytkownicy są automatycznie twoni w aplikacji. Aktywuj **Automatycznie przypisz licencje,** aby przypisać licencję do użytkownika.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Nawigatora Sprzedaży LinkedIn w Panelu dostępu należy automatycznie zalogować się do Nawigatora Sprzedaży LinkedIn, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Nawigatora sprzedaży linkedin za pomocą usługi Azure AD](https://aad.portal.azure.com/)