---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logiem jednokrotnym HRworks | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logiem jednokrotnym HRworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75638763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logiem jednokrotnym HRworks

W tym samouczku dowiesz się, jak zintegrować logowanie jednokrotne HRworks z usługą Azure Active Directory (Azure AD). Po zintegrowaniu rejestracji jednokrotnej HRworks z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do logowania jednokrotnego HRworks.
* Włącz użytkownikom automatyczne logowanie do logowania jednokrotnego HRworks za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* HRworks Jednokrotne logowanie jednokrotne (logowanie jednokrotne) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* HRworks Logowanie jednokrotne obsługuje sso inicjowane przez **sp**

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Dodawanie logowania jednokrotnego HRworks z galerii

Aby skonfigurować integrację rejestracji jednokrotnej HRworks z usługą Azure AD, należy dodać logowanie jednokrotne HRworks z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **hrworks logowanie jednokrotne w** polu wyszukiwania.
1. Wybierz **HRworks Logowanie jednokrotne** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla logowania jednokrotnego HRworks

Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD za pomocą logowania jednokrotnego HRworks przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze HRworks Jednokrotne logowanie.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego HRworks, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne HRworks](#configure-hrworks-single-sign-on-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego rejestracji jednokrotnej HRworks](#create-hrworks-single-sign-on-test-user)** — aby mieć odpowiednik B.Simon w HRworks logowanie jednokrotne, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **rejestracji jednokrotnej HRworks** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta jednokrotnego logowania HRworks,](mailto:nadja.sommerfeld@hrworks.de) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie rejestracji jednokrotnej HRworks** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do HRworks logowania jednokrotnego.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz opcję **Jednorazowe logowanie HRworks**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-hrworks-single-sign-on-sso"></a>Konfigurowanie logowania jednokrotnego HRworks

1. Aby zautomatyzować konfigurację w ramach rejestracji jednokrotnej HRworks, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Przycisk Konfigurowanie HRworks Logowanie jednokrotne** spowoduje skierowanie do aplikacji jednorazowego logowania HRworks. W tym miejscu podaj poświadczenia administratora, aby zalogować się do hrworks logowania jednokrotnego. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-4.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować funkcję rejestracji pojedynczej HRworks, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy HRworks Single Sign-On jako administrator i wykonaj następujące czynności:

1. Kliknij opcję **Logowanie** >  > **jednokrotne** zabezpieczeń Administrator**Basics** > **Security**z lewej strony paska menu i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Zaznacz pole **Użyj logowania jednokrotnego.**

    b. Wybierz **metadane XML** jako **metodę wprowadzania metadanych metadanych meta**.

    d. Wybierz **indywidualny identyfikator NameID** jako **wartość dla identyfikatora nazwy**.

    d. W Notatniku otwórz kod XML metadanych pobrany z witryny Azure Portal, skopiuj jego zawartość, a następnie wklej ją do pola tekstowego **Metadane.**

    e. Kliknij przycisk **Zapisz**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Tworzenie użytkownika testowego rejestracji jednokrotnej HRworks

Aby włączyć użytkowników usługi Azure AD, zaloguj się do HRworks logowania jednokrotnego, muszą być aprowizowane do HRworks logowania jednokrotnego. W HRworks Logowanie jednokrotne jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do HRworks Logowanie jednokrotne jako administrator.

1. Kliknij > na Osoby > **administratora** > **Osoby****Nowa osoba** z lewej strony paska menu. **Administrator**

     ![Konfigurowanie logowania jednokrotnego](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. W wyskakującym okienku kliknij przycisk **Dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. W wyskakującym okienku **Utwórz nową osobę z terminami prawnymi** wypełnij odpowiednie szczegóły, takie jak **Imię**i **nazwisko** , a następnie kliknij przycisk **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka rejestracji jednokrotnej HRworks w Panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego HRworks, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj logowanie jednokrotne HRworks za pomocą usługi Azure AD](https://aad.portal.azure.com/)