---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z thousandEyes | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373252"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z thousandEyes

W tym samouczku dowiesz się, jak zintegrować ThousandEyes z usługą Azure Active Directory (Azure AD). Po zintegrowaniu ThousandEyes z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do ThousandEyes.
* Włącz użytkownikom automatyczne logowanie się do ThousandEyes za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* ThousandEyes obsługuje **sp i idp** zainicjowane SSO
* ThousandEyes obsługuje [ **automatyczne** inicjowanie obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-thousandeyes-from-the-gallery"></a>Dodawanie ThousandEyes z galerii

Aby skonfigurować integrację ThousandEyes w usłudze Azure AD, należy dodać ThousandEyes z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ThousandEyes** w polu wyszukiwania.
1. Wybierz **pozycję ThousandEyes** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla thousandEyes

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą ThousandEyes przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w ThousandEyes.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą thousandEyes, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne ThousandEyes](#configure-thousandeyes-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego ThousandEyes](#create-thousandeyes-test-user)** — aby mieć odpowiednik B.Simon w ThousandEyes, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **ThousandEyes** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana, a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.**

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://app.thousandeyes.com/login/sso`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie ThousandEyes** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do ThousandEyes.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ThousandEyes**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-thousandeyes-sso"></a>Konfigurowanie wpisu SSO thousandEyes

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy **ThousandEyes** jako administrator.

2. W menu u góry kliknij pozycję **Settings** (Ustawienia).

    ![Ustawienia](./media/thousandeyes-tutorial/ic790066.png "Ustawienia")

3. Kliknij **pozycję Konto**

    ![Konto](./media/thousandeyes-tutorial/ic790067.png "Konto")

4. Kliknij kartę **Uwierzytelnianie & zabezpieczeń.**

    ![Uwierzytelnianie & zabezpieczeń](./media/thousandeyes-tutorial/ic790068.png "Uwierzytelnianie & zabezpieczeń")

5. W sekcji **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/ic790069.png "Konfigurowanie logowania jednokrotnego")

    a. Wybierz **włącz logowanie jednokrotne**.

    b. W polu tekstowym **URL strony logowania** wklej adres URL **logowania,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **adresu URL strony wylogowania** wklej adres URL **wylogowania,** który został skopiowany z witryny Azure Portal.

    d. Pole tekstowe **wystawcy dostawcy tożsamości,** wklej **identyfikator usługi Azure AD**, który został skopiowany z witryny Azure portal.

    e. W **obszarze Certyfikat weryfikacji**kliknij pozycję Wybierz **plik**, a następnie przekaż certyfikat pobrany z witryny Azure portal.

    f. Kliknij przycisk **Zapisz**.

### <a name="create-thousandeyes-test-user"></a>Utwórz użytkownika testowego ThousandEyes

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w ThousandEyes. ThousandEyes obsługuje automatyczne inicjowanie obsługi administracyjnej użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](thousandeyes-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy ThousandEyes jako administrator.

2. Kliknij przycisk **Ustawienia**.

    ![Ustawienia](./media/thousandeyes-tutorial/IC790066.png "Ustawienia")

3. Kliknij pozycję **Account** (Konto).

    ![Konto](./media/thousandeyes-tutorial/IC790067.png "Konto")

4. Kliknij kartę **Konta & użytkownicy.**

    ![Konta & użytkowników](./media/thousandeyes-tutorial/IC790073.png "Konta & użytkowników")

5. W sekcji **Dodawanie kont & użytkowników** wykonaj następujące czynności:

    ![Dodawanie kont użytkowników](./media/thousandeyes-tutorial/IC790074.png "Dodawanie kont użytkowników")

    a. W polu **tekstowym Nazwa** wpisz nazwę użytkownika, takiego jak **B.Simon**.

    b. W **polu tekstowym Poczta e-mail** wpisz adres e-mail użytkownika, takiego jak b.simon@contoso.com.

    b. Kliknij **pozycję Dodaj nowego użytkownika do konta**.

    > [!NOTE]
    > Posiadacz konta usługi Azure Active Directory otrzyma wiadomość e-mail z łączem potwierdzającym i aktywowanym kontem.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure Active Directory można użyć innych narzędzi do tworzenia kont użytkowników ThousandEyes lub interfejsów API dostarczonych przez ThousandEyes.


## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ThousandEyes w Panelu dostępu należy automatycznie zalogować się do pliku ThousandEyes, dla którego skonfigurowano logującą się jednoślików. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj thousandEyes z usługą Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie inicjowania obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)