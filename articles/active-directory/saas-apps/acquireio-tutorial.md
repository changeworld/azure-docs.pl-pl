---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą AcquireIO | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01bcc3678485119afae1d567d97eff9dcebe6b95
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76714622"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą AcquireIO

W tym samouczku dowiesz się, jak zintegrować acquireIO z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AcquireIO z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do AcquireIO.
* Włącz użytkownikom automatyczne logowanie do acquireIO za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego AcquireIO(SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* AcquireIO obsługuje zainicjowane przez protokół SSO inicjowane przez **protokół** IDP

## <a name="adding-acquireio-from-the-gallery"></a>Dodawanie AcquireIO z galerii

Aby skonfigurować integrację acquireIO z usługą Azure AD, należy dodać AcquireIO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AcquireIO** w polu wyszukiwania.
1. Wybierz **polecenie AcquireIO** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi AcquireIO

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu usługi AcquireIO przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze AcquireIO.

Aby skonfigurować i przetestować jednostkę SSO usługi Azure AD przy za pomocą usługi AcquireIO, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj jednostkę SSO AcquireIO](#configure-acquireio-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego AcquireIO](#create-acquireio-test-user)** — aby mieć odpowiednik B.Simon w AcquireIO, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **AcquireIO** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Otrzymasz rzeczywisty adres URL odpowiedzi, który jest wyjaśniony w dalszej części **konfigurowania AcquireIO** sekcji samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie acquireIO** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do AcquireIO.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **AcquireIO**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-acquireio-sso"></a>Konfigurowanie jednostki SYC acquireIO

1. Aby zautomatyzować konfigurację w ramach usługi AcquireIO, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij przycisk **Skonfiguruj AcquireIO**, który kieruje użytkownika do aplikacji AcquireIO. W tym miejscu podaj poświadczenia administratora, aby zalogować się do AcquireIO. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować AcquireIO, w innym oknie przeglądarki internetowej zaloguj się do AcquireIO jako administrator.

1. Po lewej stronie menu kliknij **App Store**.

     ![Konfiguracja AcquireIO](./media/acquireio-tutorial/config01.png)

1. Przewiń w dół do **usługi Active Directory** i kliknij **przycisk Zainstaluj**.

    ![Konfiguracja AcquireIO](./media/acquireio-tutorial/config02.png)

1. W wyskakującym okienku usługi Active Directory wykonaj następujące czynności:

    ![Konfiguracja AcquireIO](./media/acquireio-tutorial/config03.png)

    a. Kliknij **przycisk Kopiuj,** aby skopiować adres URL odpowiedzi dla wystąpienia i wkleić go w skrzynce tekstowej **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość adresu URL **logowania,** który został skopiowany z witryny Azure portal.

    d. Otwórz zakodowany certyfikat Base64 w Notatniku, skopiuj jego zawartość i wklej go w polu **tekstowym certyfikatu X.509.**

    d. Kliknij **pozycję Połącz teraz**.

### <a name="create-acquireio-test-user"></a>Utwórz użytkownika testowego AcquireIO

Aby umożliwić użytkownikom usługi Azure AD zalogować się do AcquireIO, muszą one być aprowizowane do AcquireIO. W AcquireIO inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. W innym oknie przeglądarki internetowej zaloguj się do AcquireIO jako administrator.

1. Po lewej stronie menu kliknij pozycję **Profile** i przejdź do pozycji **Dodaj profil**.

     ![Konfiguracja AcquireIO](./media/acquireio-tutorial/config04.png)

1. W wyskakującym okienku **Dodaj klienta** wykonaj następujące czynności:

    ![Konfiguracja AcquireIO](./media/acquireio-tutorial/config05.png)

    a. W polu tekstowym **Nazwa** wprowadź nazwę użytkownika, takiego jak **B.simon**.

    b. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład **B.simon@contoso.com**.

    d. Kliknij **przycisk Prześlij**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka AcquireIO w Panelu dostępu należy automatycznie zalogować się do acquireIO, dla którego skonfigurowano logującą się logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Spróbuj uzyskać usługi AcquireIO za pomocą usługi Azure AD](https://aad.portal.azure.com/)
