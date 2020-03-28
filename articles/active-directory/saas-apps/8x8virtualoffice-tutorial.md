---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z 8x8 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą 8x8.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c598222978a1c831be6f5e9db9eb87b2d6b6b96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968641"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z 8x8

W tym samouczku dowiesz się, jak zintegrować 8x8 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu 8x8 z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do 8x8.
* Włącz użytkownikom automatyczne logowanie do 8x8 za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja 8x8.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* 8x8 obsługuje **sp i idp** zainicjowane SSO

* Po skonfigurowaniu 8x8 można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-8x8-from-the-gallery"></a>Dodawanie 8x8 z galerii

Aby skonfigurować integrację 8x8 z usługą Azure AD, należy dodać 8x8 z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **8x8** w polu wyszukiwania.
1. Wybierz **8x8** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla 8x8

Konfigurowanie i testowanie usługi Azure AD SSO z 8x8 przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w 8x8.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z 8x8, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj 8x8 SSO](#configure-8x8-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego 8x8](#create-8x8-test-user)** — aby mieć odpowiednik B.Simon w 8x8, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **8x8** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL:`https://sso.8x8.com/saml2`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://sso.8x8.com/saml2`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze. Certyfikat będzie używany w dalszej części samouczka w sekcji **Konfigurowanie 8x8 SSO.**

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie 8x8** skopiuj adresy URL, a te wartości adresów URL będą używane w dalszej części samouczka.

    ![Kopiowanie adresów URL konfiguracji](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do 8x8.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **8x8**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-8x8-sso"></a>Konfigurowanie 8x8 SSO

Następna część samouczka zależy od tego, jaki rodzaj subskrypcji masz z 8x8.

* W przypadku klientów korzystających z programu Configuration Manager 8x8 i X przy użyciu programu Configuration Manager w celu administrowania można znaleźć w temacie [Konfigurowanie programu Configuration Manager 8x8](#configure-8x8-configuration-manager).
* W przypadku klientów pakietu Virtual Office korzystających z Menedżera kont do administrowania zobacz [Konfigurowanie Menedżera kont 8x8](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Konfigurowanie programu 8x8 Configuration Manager

1. Zaloguj się do programu 8x8 [Configuration Manager](https://vo-cm.8x8.com/).

1. Ze strony głównej kliknij pozycję **Zarządzanie tożsamościami**.

    ![8x8 Menedżer konfiguracji](./media/8x8virtualoffice-tutorial/configure1.png)

1. Zaznacz **opcję Logowanie jednokrotne (Logowanie jednokrotne),** a następnie wybierz pozycję **Microsoft Azure AD**.

    ![8x8 Menedżer konfiguracji](./media/8x8virtualoffice-tutorial/configure2.png)

1. Skopiuj trzy adresy URL i certyfikat podpisywania ze strony **Konfigurowanie logowania jednokrotnego z saml** w usłudze Azure AD do sekcji **Ustawienia SAML usługi Microsoft Azure AD** w programie 8x8 Configuration Manager.

    ![8x8 Menedżer konfiguracji](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Skopiuj **adres URL logowania** do adresu URL logowania do protokołu **IDP**.

    b. Skopiuj **identyfikator usługi Azure AD** do adresu **URL/URN wystawcy IDP**.

    d. Kopiuj **adres URL wylogowania** do **adresu URL wylogowania IDP**.

    d. Pobierz **certyfikat (Base64)** i prześlij go do **certyfikatu**.

    e. Kliknij przycisk **Zapisz**.

### <a name="configure-8x8-account-manager"></a>Konfigurowanie menedżera kont 8x8

1. Zaloguj się do swojej dzierżawy usługi 8x8 Virtual Office jako administrator.

1. Na panelu aplikacji wybierz pozycję **Menedżer konta usługi 8x8 Virtual Office**.

    ![Konfigurowanie po stronie aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Wybierz konto **firmowe**, którym chcesz zarządzać, a następnie kliknij przycisk **Zaloguj się**.

    ![Konfigurowanie po stronie aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Kliknij kartę **KONTA** na liście menu.

    ![Konfigurowanie po stronie aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Kliknij pozycję **Logowanie jednokrotne** na liście kont.

    ![Konfigurowanie po stronie aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. W obszarze Metody uwierzytelniania wybierz pozycję **Logowanie jednokrotne** i kliknij pozycję **SAML**.

    ![Konfigurowanie po stronie aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. W sekcji **Logowanie jednokrotne SAML** wykonaj następujące kroki:

    ![Konfigurowanie po stronie aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. Kliknij przycisk **Przeglądaj**, aby przekazać certyfikat pobrany z witryny Azure Portal.

    e. Kliknij przycisk **Zapisz**.

### <a name="create-8x8-test-user"></a>Tworzenie użytkownika testowego 8x8

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w 8x8. Pracuj z [zespołem pomocy technicznej 8x8,](https://www.8x8.com/about-us/contact-us) aby dodać użytkowników na platformie 8x8. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka 8x8 w Panelu dostępu należy automatycznie zalogować się do 8x8, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj 8x8 z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić 8x8 dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)