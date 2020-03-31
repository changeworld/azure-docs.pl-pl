---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z uprzężą | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Harness.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21409eb056743d92db42e0787af24f8cec07db1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z uprzężą

W tym samouczku dowiesz się, jak zintegrować uprząż z usługą Azure Active Directory (Azure AD). Po zintegrowaniu uprzęży z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do uprzęży.
* Włącz użytkownikom automatyczne logowanie do wykorzystania za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Korzyszej subskrypcję z włączoną funkcją logowania jednokrotnego.Przewody przewodzone single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Uprząż obsługuje **sp i IDP** zainicjowane SSO

## <a name="adding-harness-from-the-gallery"></a>Dodawanie uprzęży z galerii

Aby skonfigurować integrację uprzęży z usługą Azure AD, należy dodać uprząż z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Uprząż** w polu wyszukiwania.
1. Wybierz **pozycję Uprząż** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla uprzęży

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą uprzęży przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w uprzęży.

Aby skonfigurować i przetestować usługę Azure AD SSO z elementami wiązki przewodów, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne uprzęży](#configure-harness-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego uprzęży](#create-harness-test-user)** — aby mieć odpowiednik B.Simon w uprzęży, która jest połączona z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Harness** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego.**
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://app.harness.io/`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Otrzymasz rzeczywisty adres URL odpowiedzi z sekcji **Konfigurowanie wiązki SSO uprzęży,** co wyjaśniono w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie wiązki przewodów** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do uprzęży.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz opcję **Uprząż**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-harness-sso"></a>Konfigurowanie sytchodnie wiązki wiązki

1. Aby zautomatyzować konfigurację w uprzęży, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **na Uprząż instalatora** będzie kierować cię do aplikacji Harness. Stamtąd podaj poświadczenia administratora, aby zalogować się do Uprzęży. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować uprzężenia, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Harness jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij pozycję**Ustawienia uwierzytelniania zarządzania dostępem** **do bezpieczeństwa** > **Access Management** > ciągłego .

    ![Konfiguracja uprzęży](./media/harness-tutorial/configure01.png)

5. W sekcji **Dostawcy jednopisu** kliknij **przycisk + Dodaj dostawców jednopisu** > **SAML**.

    ![Konfiguracja uprzęży](./media/harness-tutorial/configure03.png)

6. W wyskakującym okienku **DOSTAWCY SAML** wykonaj następujące czynności:

    ![Konfiguracja uprzęży](./media/harness-tutorial/configure02.png)

    a. Skopiuj **w dostawcy logowania sytuacyjnego włącz logowanie oparte na SAML, a następnie wprowadź następujące** wystąpienie adresu URL i wklej go w skrzynce tekstowej Adres URL odpowiedzi w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. W polu **tekstowym Nazwa wyświetlana** wpisz nazwę wyświetlaną.

    d. Kliknij **pozycję Wybierz plik,** aby przekazać plik XML metadanych federacji, który został pobrany z usługi Azure AD.

    d. Kliknij przycisk **PRZEŚLIJ**.

### <a name="create-harness-test-user"></a>Utwórz użytkownika testowego uprzęży

Aby umożliwić użytkownikom usługi Azure AD zalogować się do uprzęży, muszą one być aprowiowane w uprzęży. W uprzęży inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do uprzęży jako administrator.

1. W prawym górnym rogu strony kliknij pozycję**Użytkownicy**zarządzania **ciągłym** > **dostępem** > do zabezpieczeń .

    ![Konfiguracja uprzęży](./media/harness-tutorial/configure04.png)

1. Po prawej stronie kliknij przycisk **+ Dodaj użytkownika**.

    ![Konfiguracja uprzęży](./media/harness-tutorial/configure05.png)

1. W wyskakującym okienku **Dodaj użytkownika** wykonaj następujące czynności:

    ![Konfiguracja uprzęży](./media/harness-tutorial/configure06.png)

    a. W polu **tekstowym Adres e-mail** wprowadź adres `B.simon@contoso.com`e-mail użytkownika w stylu .

    b. Wybierz grupy **użytkowników**.

    d. Kliknij **przycisk Prześlij**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Wiązki przewodów w Panelu dostępu należy automatycznie zalogować się do uprzęży, dla której skonfigurowano logującą się logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj uprząż dzięki usłudze Azure AD](https://aad.portal.azure.com/)

