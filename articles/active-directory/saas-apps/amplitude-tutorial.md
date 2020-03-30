---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z amplitudą | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Amplitude.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d077da34a6e82ced957c4da1e6abf7a5e294e78
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72596253"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amplitude"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z amplitudą

W tym samouczku dowiesz się, jak zintegrować amplitudę z usługą Azure Active Directory (Azure AD). Po zintegrowaniu amplitudy z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do amplitudy.
* Włącz użytkownikom automatyczne logowanie do amplitudy za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej amplitudy.Amplitude single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja Amplitude obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**
* Aplikacja Amplitude obsługuje aprowizowanie użytkowników typu **just in time**

## <a name="adding-amplitude-from-the-gallery"></a>Dodawanie aplikacji Amplitude z galerii

Aby skonfigurować integrację aplikacji Amplitude z usługą Azure AD, musisz dodać aplikację Amplitude z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amplituda** w polu wyszukiwania.
1. Wybierz **amplitudę** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amplitude"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD pod kątem amplitudy

Konfigurowanie i testowanie usługi Azure AD SSO z amplitudą przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w amplitudzie.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z amplitudą, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj SSO amplitudy](#configure-amplitude-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego amplitudy](#create-amplitude-test-user)** — aby mieć odpowiednik B.Simon w amplitudzie, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Amplituda** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL:`https://amplitude.com/saml/sso/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://analytics.amplitude.com/saml/sso/<uniqueid>`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Wartość adresu URL odpowiedzi uzyskasz w dalszej części tego samouczka.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://analytics.amplitude.com/sso`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie amplitudy** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do amplitudy.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Amplitude**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-amplitude-sso"></a>Konfigurowanie sydydydy amplitudy

1. Aby zautomatyzować konfigurację w amplitudzie, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Set up Amplituda** przekieruje Cię do aplikacji Amplituda. W tym miejscu podaj poświadczenia administratora, aby zalogować się do amplitudy. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować amplitudę, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Amplituda jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **Plan Admin** (Administrator planu) na pasku nawigacyjnym po lewej stronie.

    ![Konfigurowanie logowania jednokrotnego](./media/amplitude-tutorial/configure1.png)

1. Wybierz pozycję **Microsoft Azure Active Directory Metadata** (Metadane usługi Microsoft Azure Active Directory) z listy **SSO Integration** (Integracja logowania jednokrotnego).

    ![Konfigurowanie logowania jednokrotnego](./media/amplitude-tutorial/configure2.png)

1. W sekcji **Set Up Single Sign-On** (Konfigurowanie logowania jednokrotnego) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/amplitude-tutorial/configure3.png)

    a. Otwórz **plik XML metadanych** pobrany z witryny Azure Portal w programie Notatnik i wklej jego zawartość w polu tekstowym **Microsoft Azure Active Directory Metadata** (Metadane usługi Microsoft Azure Active Directory).

    b. Skopiuj wartość **Reply URL (ACS)** (Adres URL odpowiedzi — ACS) i wklej ją w polu tekstowym **Adres URL odpowiedzi** sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    d. Kliknij **przycisk Zapisz**

### <a name="create-amplitude-test-user"></a>Tworzenie użytkownika testowego aplikacji Amplitude

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w amplitudy. Aplikacja Amplitude obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Amplitude, zostanie utworzony po uwierzytelnieniu.

> [!Note]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej amplitudy](https://amplitude.zendesk.com).

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Amplitude na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Amplitude, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj amplitudę za pomocą usługi Azure AD](https://aad.portal.azure.com/)