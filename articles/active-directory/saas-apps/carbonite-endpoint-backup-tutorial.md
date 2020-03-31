---
title: 'Samouczek: Integracja usługi Azure Active Directory z tworzeniem kopii zapasowej punktów końcowych carbonite | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a tworzeniem kopii zapasowej punktów końcowych carbonite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68879667"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Samouczek: Integracja kopii zapasowej punktów końcowych carbonite z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować kopię zapasową carbonite endpoint z usługą Azure Active Directory (Azure AD). Po zintegrowaniu kopii zapasowej punktów końcowych carbonite z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do kopii zapasowej punktu końcowego carbonite.
* Włącz użytkownikom automatyczne logowanie do kopii zapasowej carbonite endpoint za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną obsługą pojedynczego logowania carbonite Endpoint Backup (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Kopia zapasowa punktów końcowych carbonite obsługuje jednostki SSO inicjowane przez sp **i idp**

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Dodawanie kopii zapasowej punktu końcowego carbonite z galerii

Aby skonfigurować integrację kopii zapasowej carbonite endpoint do usługi Azure AD, należy dodać kopię zapasową carbonite endpoint z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Carbonite Endpoint Backup** w polu wyszukiwania.
1. Wybierz **kopię zapasową carbonite endpoint** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą kopii zapasowej punktu końcowego carbonite przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w kopii zapasowej punktu końcowego carbonite.

Aby skonfigurować i przetestować usługę Azure AD SSO przy pomocą kopii zapasowej punktu końcowego carbonite, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne kopii zapasowej punktu końcowego carbonite](#configure-carbonite-endpoint-backup-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego kopii zapasowej punktu końcowego carbonite](#create-carbonite-endpoint-backup-test-user)** — aby mieć odpowiednik B.Simon w kopii zapasowej punktu końcowego carbonite, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Carbonite Endpoint Endpoint Backup** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz jeden z następujących adresów URL:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. W polu tekstowym **Odpowiedz adres URL** wpisz jeden z następujących adresów URL:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz jeden z następujących adresów URL:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie kopii zapasowej punktów końcowych carbonite** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Konfigurowanie uprawnienia sytego do wykonywania kopii zapasowych punktów końcowych carbonite

1. Aby zautomatyzować konfigurację w kopii zapasowej carbonite endpoint, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator Carbonite Endpoint Backup** spowoduje skierowanie do aplikacji Carbonite Endpoint Backup. W tym miejscu podaj poświadczenia administratora, aby zalogować się do kopii zapasowej punktu końcowego carbonite. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować kopię zapasową carbonite endpoint, otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy Carbonite Endpoint Backup jako administrator i wykonaj następujące czynności:

4. Kliknij **firmę** w lewym okienku.

    ![Konfiguracja kopii zapasowej punktu końcowego carbonite ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Kliknij **opcję Logowanie jednokrotne**.

    ![Konfiguracja kopii zapasowej punktu końcowego carbonite ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Kliknij **pozycję Włącz,** a następnie kliknij pozycję **Edytuj ustawienia,** aby skonfigurować.

    ![Konfiguracja kopii zapasowej punktu końcowego carbonite ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Na stronie Ustawienia **logowania jednokrotnego** wykonaj następujące czynności:

    ![Konfiguracja kopii zapasowej punktu końcowego carbonite ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. W polu tekstowym **nazwa dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    1. W polu tekstowym **adresu URL dostawcy tożsamości** wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.

    1. Kliknij **wybierz plik,** aby przekazać pobrany plik **certyfikatu (Base64)** z witryny Azure portal.

    1. Kliknij przycisk **Zapisz**.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do kopii zapasowej punktu końcowego carbonite.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Carbonite Endpoint Backup**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-carbonite-endpoint-backup-test-user"></a>Utwórz użytkownika testowego kopii zapasowej punktu końcowego carbonite

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Carbonite Endpoint Backup jako administrator.

1. Kliknij **pozycję Użytkownicy** z lewego okienka, a następnie kliknij pozycję **Dodaj użytkownika**.

    ![Dodawanie użytkownika w kopii zapasowej punktu końcowego carbonite](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Na stronie **Dodaj użytkownika** wykonaj następujące czynności:

    ![Dodawanie użytkownika w kopii zapasowej punktu końcowego carbonite](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Wprowadź **adres e-mail,** **imię**, **nazwisko** użytkownika i podać wymagane uprawnienia dla użytkownika zgodnie z wymaganiami organizacyjnymi.

    1. Kliknij **pozycję Dodaj użytkownika**.

### <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Kopia zapasowa carbonite endpoint w Panelu dostępu należy automatycznie zalogować się do kopii zapasowej carbonite endpoint, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)