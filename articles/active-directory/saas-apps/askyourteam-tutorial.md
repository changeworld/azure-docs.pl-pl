---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z askyourteam | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a AskYourTeam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cef6764-de54-4920-b0ad-e560c214c72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403f39de1b7d226d9feeb33388c32f63271fdcd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968533"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z askyourteam

W tym samouczku dowiesz się, jak zintegrować AskYourTeam z usługą Azure Active Directory (Azure AD). Po zintegrowaniu AskYourTeam z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do AskYourTeam.
* Włącz użytkownikom automatyczne logowanie do AskYourTeam za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego AskYourTeam (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* AskYourTeam obsługuje **sp i IDP** zainicjowane SSO.
* Po skonfigurowaniu AskYourTeam można wymusić kontrolę sesji, która chroni eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>Dodawanie AskYourTeam z galerii

Aby skonfigurować integrację AskYourTeam z usługą Azure AD, należy dodać AskYourTeam z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AskYourTeam** w polu wyszukiwania.
1. Wybierz **pozycję AskYourTeam** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla askyourteam

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą AskYourTeam przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w AskYourTeam.

Aby skonfigurować i przetestować swo przysłów YO usługi Azure AD za pomocą AskYourTeam, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj AskYourTeam SSO](#configure-askyourteam-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz askyourteam użytkownika testowego](#create-askyourteam-test-user)** — mieć odpowiednik B.Simon w AskYourTeam, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **AskYourTeam** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego adresu URL odpowiedzi i wartości adresu URL logowania, które zostały wyjaśnione w dalszej części samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie AskYourTeam** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do AskYourTeam.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **askyourteam**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-askyourteam-sso"></a>Konfigurowanie askyourteam sso

1. W innym oknie przeglądarki internetowej zaloguj się na stronie AskYourTeam jako administrator.

1. Kliknij **pozycję Moja organizacja**.

    ![Konfiguracja AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Kliknij na **Integracje**.

    ![Konfiguracja AskYourTeam](./media/askyourteam-tutorial/configure1.png)

1. Kliknij **pozycję Edytuj ustawienia**.

    ![Konfiguracja AskYourTeam](./media/askyourteam-tutorial/configure2.png)

1. Na stronie **Edytowanie integracji logowania jednokrotnego** wykonaj następujące czynności: 

    ![Konfiguracja AskYourTeam](./media/askyourteam-tutorial/configure3.png)

    a. W polu tekstowym **adresu URL usługi logowania jednokrotnego SAML** wklej wartość adresu URL **logowania** skopiowaną z witryny Azure portal.

    b. W polu tekstowym **identyfikator jednostki SAML** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    d. W polu tekstowym **adresu URL wylogowywania** wklej wartość **adresu URL wylogowania** skopiowaną z witryny Azure portal.

    d. Otwórz pobrany **certyfikat (Base64)** z witryny Azure portal w Notatniku i wklej zawartość do pola tekstowego **SAML Signing Certificate — Base64.**

    > [!NOTE]
    > Alternatywnie można również przekazać plik **XML metadanych federacji,** klikając opcję **Wybierz plik.**

    e. Kopiuj wartość **adresu URL odpowiedzi (adres URL usługi konsumenta potwierdzenia),** wklej tę wartość w polu tekstowym **Odpowiedz adres URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    f. Kopiuj wartość **adresu URL logowania,** wklej tę wartość w polu tekstowym **Podpisz adres URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    g. Kliknij przycisk **Zapisz**.

### <a name="create-askyourteam-test-user"></a>Utwórz użytkownika testowego AskYourTeam

1. W innym oknie przeglądarki internetowej zaloguj się na stronie AskYourTeam jako administrator.

1. Kliknij **pozycję Moja organizacja**.

    ![Konfiguracja AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Kliknij **pozycję Użytkownicy** i wybierz pozycję **Nowy użytkownik**.

    ![Konfiguracja AskYourTeam](./media/askyourteam-tutorial/user2.png)

1. W sekcji **Nowy użytkownik** wykonaj następujące czynności:

    ![Konfiguracja AskYourTeam](./media/askyourteam-tutorial/user3.png)

    1. W polach tekstowych **Imię** wprowadź imię użytkownika.

    1. W polach tekstowych **Nazwisko** wprowadź nazwisko użytkownika.

    1. W polu **tekstowym Wiadomość e-mail** wpisz adres B.Simon@contoso.come-mail użytkownika w stylu .

    1. Wybierz **rolę** dla użytkownika zgodnie z wymaganiami organizacji.

    1. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka AskYourTeam w Panelu dostępu należy automatycznie zalogować się do AskYourTeam, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj AskYourTeam z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
