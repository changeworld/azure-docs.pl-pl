---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z zabezpieczeniami folii tinfoil | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74170761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z zabezpieczeniami usługi TINFOIL

W tym samouczku dowiesz się, jak zintegrować zabezpieczenia tinfoil z usługą Azure Active Directory (Azure AD). Po zintegrowaniu zabezpieczeń folii tinfoil z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do zabezpieczeń folii aluminiowej.
* Włącz użytkownikom automatyczne logowanie do aplikacji TINFOIL SECURITY za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) usługi TINFOIL SECURITY.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* TINFOIL SECURITY obsługuje zainicjowane przez protokół SSO protokołu **IDP**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Dodawanie TINFOIL SECURITY z galerii

Aby skonfigurować integrację zabezpieczeń folii tinfoil do usługi Azure AD, należy dodać TINFOIL SECURITY z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **TINFOIL SECURITY** w polu wyszukiwania.
1. Wybierz **pozycję TINFOIL SECURITY** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi TINFOIL SECURITY

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą funkcji TINFOIL SECURITY przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sygnował, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w tinfoil security.

Aby skonfigurować i przetestować usługę Azure AD SSO przy pomocą tinfoil security, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie logowania systemu IDENTYFIKACJI SYSTEMU IDENTYFIKACJI UŻYTKOWNIKA SYSTEMU IDENTYFIKACJI](#configure-tinfoil-security-sso)** UŻYTKOWNIKA SYSTEMU IDENTYFIKACJI UŻYTKOWNIKA SYSTEMU IDENTYFIKACJI UŻYTKOWNIKA W celu skonfigurowania ustawień logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego ZABEZPIECZEŃ TINFOIL](#create-tinfoil-security-test-user)** — aby mieć odpowiednik B.Simon w TINFOIL SECURITY, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **TINFOIL SECURITY** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana, a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.**

1. Aplikacja Visitly oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji Visitly oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy |
    | ------------------- | -------------|
    | accountId | UXXXXXXXXXXXXXXX |

    > [!NOTE]
    > Otrzymasz wartość accountid wyjaśnione w dalszej części samouczka.

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **Certyfikat podpisywania SAML** skopiuj **wartość odcisku palca** i zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie zabezpieczeń folii tinfoil** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do tinfoil zabezpieczeń.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **TINFOIL SECURITY**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-tinfoil-security-sso"></a>Konfigurowanie sygnowania zabezpieczeń bezpieczeństwa folii aluminiowej

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy TINFOIL SECURITY jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Moje konto**.

    ![Pulpit nawigacyjny](./media/tinfoil-security-tutorial/ic798971.png "Pulpit nawigacyjny")

1. Kliknij pozycję **Security** (Zabezpieczenia).

    ![Zabezpieczenia](./media/tinfoil-security-tutorial/ic798972.png "Zabezpieczenia")

1. Na stronie konfiguracji **Single Sign On** (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/tinfoil-security-tutorial/ic798973.png "Logowanie jednokrotne")

    a. Wybierz pozycję **Enable SAML** (Włącz SAML).

    b. Kliknij pozycję **Konfiguracja ręczna**.

    d. W polu tekstowym **ADRESU URL wpisu SAML** wklej wartość **adresu URL logowania** skopiowanego z witryny Azure portal

    d. W polu tekstowym **odcisków palców certyfikatu SAML** wklej wartość **odcisku palca** skopiowaną z sekcji **Certyfikat podpisywania SAML.**
  
    e. Skopiuj wartość **identyfikatora konta** i wklej wartość w polu tekstowym **Atrybut źródła** w sekcji **Atrybuty & oświadczeń użytkownika** w witrynie Azure portal.

    f. Kliknij przycisk **Zapisz**.

### <a name="create-tinfoil-security-test-user"></a>Utwórz użytkownika testowego TINFOIL SECURITY

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do tinfoil security, muszą one być aprowizowane do TINFOIL SECURITY. W przypadku TINFOIL SECURITY inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby uzyskać aprowizję użytkownika, wykonaj następujące kroki:**

1. Jeśli użytkownik jest częścią konta Enterprise, należy [skontaktować się z zespołem pomocy technicznej TINFOIL SECURITY,](https://www.tinfoilsecurity.com/contact) aby utworzyć konto użytkownika.

1. Jeśli użytkownik jest zwykłym użytkownikiem systemu SaaS zabezpieczeń tinfoil, użytkownik może dodać współpracownika do dowolnej witryny użytkownika. Spowoduje to wyzwolenie procesu wysyłania zaproszenia do określonej wiadomości e-mail w celu utworzenia nowego konta użytkownika TINFOIL SECURITY.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia kont użytkowników zabezpieczeń tinfoil lub interfejsów API dostarczonych przez tinfoil security.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka TINFOIL SECURITY w Panelu dostępu należy automatycznie zalogować się do programu TINFOIL SECURITY, dla którego skonfigurowano logowanie jednośmigłowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj TINFOIL SECURITY w usłudze Azure AD](https://aad.portal.azure.com/)