---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z claromentis | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Claromentis.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7980e0c5-e4d8-4678-afa2-7ec219432114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6eaf23950f83592a33709574be464f4499f0ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74823221"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z claromentis

W tym samouczku dowiesz się, jak zintegrować system Claromentis z usługą Azure Active Directory (Azure AD). Po zintegrowaniu claromentis z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Claromentis.
* Włącz użytkownikom automatyczne logowanie do aplikacji Claromentis za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) firmy Claromentis.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Claromentis obsługuje **sp i IDP** zainicjowane SSO
* Claromentis obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-claromentis-from-the-gallery"></a>Dodawanie Claromentis z galerii

Aby skonfigurować integrację programu Claromentis z usługą Azure AD, należy dodać program Claromentis z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **polecenie Claromentis** w polu wyszukiwania.
1. Wybierz **polecenie Claromentis** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla platformy Claromentis

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą narzędzia Claromentis przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Claromentis.

Aby skonfigurować i przetestować usługę Azure AD SSO z claromentis, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne Claromentis](#configure-claromentis-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Claromentis](#create-claromentis-test-user)** — aby mieć odpowiednik B.Simon w Claromentis, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Claromentis** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wprowadź wartość identyfikatora zgodnie z wymaganiami organizacji.

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 

    | | |
    |-|-|
    | `https://<customer_site_url>/login`|
    | `https://<customer_site_url>/login?no_auto=0`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, który został wyjaśniony w dalszej części turorial.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie claromentis skopiuj** odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Claromentis.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz polecenie **Claromentis**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-claromentis-sso"></a>Konfigurowanie sytuacji sytuacyja sytuacyjego Claromentis

1. W innym oknie przeglądarki zaloguj się na stronie claromentis jako administrator.

1. Kliknij **ikonę aplikacji** i wybierz pozycję **Administrator**.

    ![Konfiguracja Claromentis](./media/claromentis-tutorial/config1.png)

1. Wybierz **kartę Niestandardowy program obsługi logowania.**

    ![Konfiguracja Claromentis](./media/claromentis-tutorial/config2.png)

1. Wybierz **SAML Config**.

    ![Konfiguracja Claromentis](./media/claromentis-tutorial/config3.png)

1. Na karcie **Konfiguracie SAML** przewiń w dół do sekcji **Konfiguracja** i wykonaj następujące czynności:

    ![Konfiguracja Claromentis](./media/claromentis-tutorial/config4.png)

    a. W **polach tekstowych Nazwa kontaktu technicznego** wprowadź nazwisko osoby kontaktowej technicznej.

    b. W skrzynce tekstowej **Kontakt techniczny wprowadź** adres e-mail osoby kontaktowej.

    d. Podaj hasło w pola **tekstowym Hasło administratora** uwierzytelniania.

1. Przewiń w dół do **źródła eru** i wykonaj następujące czynności:

    ![Konfiguracja Claromentis](./media/claromentis-tutorial/config5.png)

    a. W polu tekstowym **IDP** wprowadź wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    b. W polu **tekstowym Identyfikator jednostki** wprowadź wartość identyfikatora jednostki.

    d. Przekaż plik **XML metadanych federacji,** który został pobrany z witryny Azure portal.

    d. Kliknij przycisk **Zapisz**.

1. Teraz zauważysz, że wszystkie adresy URL zostały wypełnione w sekcji **Dostawca tożsamości** w sekcji **SAML Config.**

    ![Konfiguracja Claromentis](./media/claromentis-tutorial/config6.png)

    a. Skopiuj wartość **identyfikatora (identyfikatora jednostki),** wklej tę wartość w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. Skopiuj wartość **adresu URL odpowiedzi,** wklej tę wartość w polu tekstowym **Odpowiedz adres URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    d. Kopiuj wartość **adresu URL logowania,** wklej tę wartość w polu tekstowym **Adresu URL logowania w** sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

### <a name="create-claromentis-test-user"></a>Utwórz użytkownika testowego Claromentis

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Claromentis. Claromentis obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w claromentis, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Claromentis w Panelu dostępu należy automatycznie zalogować się do claromentis, dla którego skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Claromentis z usługą Azure AD](https://aad.portal.azure.com/)