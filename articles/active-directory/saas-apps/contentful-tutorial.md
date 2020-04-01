---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z contentful | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a contentful.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f29e1015-d508-4698-a381-5d871c646161
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd218c61114c1e15009ace5a9a9bd7a536996e86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72968672"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z contentful

W tym samouczku dowiesz się, jak zintegrować zawartość z usługą Azure Active Directory (Azure AD). Po zintegrowaniu contentful z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do Contentful.
* Włącz użytkownikom automatyczne logowanie do contentful z ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja obsługująta przez logowanie jednokrotne.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Obsługa zgodnego **z treścią identyfikatorów** SSO inicjowanych przez sp i protokół IDP
* Obsługa obsługi obsługi **inicjowania** obsługi administracyjnej przez użytkowników just in time

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu. Tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-contentful-from-the-gallery"></a>Dodawanie treści z galerii

Aby skonfigurować integrację Contentful do usługi Azure AD, należy dodać Contentful z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Contentful** w polu wyszukiwania.
1. Wybierz **opcję Contentful** w wynikach, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla treści

Konfigurowanie i testowanie usługi Azure AD SSO z contentful przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w contentful.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z zawartością, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj zgodne logowanie —](#configure-contentful-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz contentful użytkownika testowego](#create-contentful-test-user)** — mieć odpowiednik B.Simon w Contentful, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Contentful** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    - W polu tekstowym **Odpowiedz adres URL** skopiuj adres URL usługi ACS (Asercja Consumer Service) ze strony konfiguracji usługi SSO w contentful. Będzie to wyglądać tak:`https://be.contentful.com/sso/<organization_id>/consume`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    - W polu **tekstowym Sign-on URL** skopiuj ten sam adres URL usługi ACS (Asercja Konsumenta). Będzie to wyglądać tak:`https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego adresu URL odpowiedzi i adresu URL logowania, kopiując adres URL usługi ACS (Asercja Konsumenta) ze strony konfiguracji logowania do logowania w contentful.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie zawartości** skopiuj adres URL logowania, aby skonfigurować zgodne z logowaniem.

    ![Kopiowanie adresów URL konfiguracji](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. W lewym okienku w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Contentful.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Contentful**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu strony.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu strony.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-contentful-sso"></a>Konfigurowanie zgodnego dostawcy systemu sytego

Wykonaj następujące kroki, aby skonfigurować logowanie jednokrotne po stronie **Contentful.**

1. W [polu Contentful](https://app.contentful.com)przejdź do strony konfiguracji logowania sygodnego w **obszarze Ustawienia organizacji**.
1. Kliknij przycisk **SSO .**
1. Skopiuj i wklej adres URL logowania z sekcji **Konfigurowanie zawartości** w usłudze Azure AD.
1. Skopiuj i wklej certyfikat z pliku certyfikatu Base64 pobranego z usługi Azure AD.
1. Skonfiguruj nazwę logowania typu SSO dla logowania inicjowanego przez dodatek SP.
1. Kliknij **włącz jedno i przesuń jedno i za mało.**

Jeśli to nie zadziała, skontaktuj się z [zespołem pomocy technicznej Contentful](mailto:support@contentful.com).

### <a name="create-contentful-test-user"></a>Tworzenie zadowolonego użytkownika testowego

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Contentful. Contentful obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Contentful, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Contentful w Panelu dostępu należy automatycznie zalogować się do contentful, dla którego skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj zawartość usługi Azure AD](https://aad.portal.azure.com/)
