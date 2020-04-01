---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z programem Watch by Colors | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem Watch by Colors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fdc8ba2337a3be49a4645e48a45120fb0ccec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026091"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z programem Watch by Colors

W tym samouczku dowiesz się, jak zintegrować program Watch by Colors z usługą Azure Active Directory (Azure AD). Po zintegrowaniu watch by colors z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do watch by colors.
* Włącz użytkownikom automatyczne logowanie do usługi Watch by Colors za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Watch by Colors subskrypcja z włączoną funkcją logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Watch by Colors obsługuje jednostki SSO inicjowane przez **SP i IDP**

## <a name="adding-watch-by-colors-from-the-gallery"></a>Dodawanie zegarka według kolorów z galerii

Aby skonfigurować integrację programu Watch by Colors z usługą Azure AD, należy dodać program Watch by Colors z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania wpisz **Obserwuj według kolorów.**
1. Wybierz **pozycję Obserwuj według kolorów** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu Watch by Colors

Konfigurowanie i testowanie usługi Azure AD SSO z programem Watch by Colors przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Watch by Colors.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z programem Watch by Colors, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne zegarka według kolorów](#configure-watch-by-colors-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz watch by colors użytkownika testowego](#create-watch-by-colors-test-user)** — mieć odpowiednik B.Simon w watch by colors, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Watch by Colors** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana w trybie inicjowanym **przez usługę IDP,** a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.**

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://app.colorscorporation.com/login`

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do watch by colors.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Obserwuj według kolorów**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-watch-by-colors-sso"></a>Konfigurowanie czujki według sytua sytua kolorów

1. Aby zautomatyzować konfigurację w programie Watch by Colors, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **na ustawienia Watch by Colors** spowoduje skierowanie do aplikacji Watch by Colors. W tym miejscu podaj poświadczenia administratora, aby zalogować się do programu Watch by Colors. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-5.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Watch by Colors, otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy Watch by Colors jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij **opcję** > **Ustawienia** > konta profilu**Logowanie jednokrotne (logowanie jednokrotne).**

    ![Konfiguracja zegarka według kolorów](./media/watch-by-colors-tutorial/config01.png)

5. Na stronie **logowania jednokrotnego (logowanie jednokrotne)** wykonaj następujące czynności:

    ![Konfiguracja zegarka według kolorów](./media/watch-by-colors-tutorial/config02.png)

    a. Przełącz **włącz saml** na **wł.**

    b. W polu tekstowym **adresu URL** wklej adres **URL metadanych federacji,** który został skopiowany z witryny Azure Portal.

    d. Kliknij **przycisk Importuj**, a następnie następujące pola automatycznie wypełniają się automatycznie na stronie.

    d. Kliknij przycisk **Zapisz**.

### <a name="create-watch-by-colors-test-user"></a>Utwórz użytkownika testowego Zegarek według kolorów

Aby umożliwić użytkownikom usługi Azure AD zalogować się do watch by colors, muszą one być aprowizowane do watch przez kolory. W watch by colors inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby obserwować według kolorów jako administrator zabezpieczeń.

1. W prawym górnym rogu strony kliknij **profil** > **Użytkownicy Dodaj** > **użytkownika**.

    ![Konfiguracja zegarka według kolorów](./media/watch-by-colors-tutorial/config03.png)

1. Na stronie **Szczegóły użytkownika** wykonaj następujące czynności:

    ![Konfiguracja zegarka według kolorów](./media/watch-by-colors-tutorial/config04.png)

    a. W polu **tekstowym Imię** wprowadź imię użytkownika, takiego jak **B**.

    b. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, takiego jak **Simon**.

    d. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `B.Simon@contoso.com`.

    d. W polu **tekstowym Hasło** wprowadź hasło.

    e. Wybierz **pozycję Uprawnienia do konta** zgodnie z organizacją.

    f. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Obserwuj według kolorów w Panelu dostępu należy automatycznie zalogować się do programu Watch by Colors, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Watch by Colors dzięki usłudze Azure AD](https://aad.portal.azure.com/)

