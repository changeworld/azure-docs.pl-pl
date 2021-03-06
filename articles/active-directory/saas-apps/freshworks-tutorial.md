---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) ze freshworks | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a freshworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72376413"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) ze freshworks

W tym samouczku dowiesz się, jak zintegrować Freshworks z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Freshworks z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Freshworks.
* Włącz użytkownikom automatyczne logowanie do freshworks za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego (SSO) firmy Freshworks.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Freshworks **obsługuje** sp zainicjowane SSO

## <a name="adding-freshworks-from-the-gallery"></a>Dodawanie Freshworks z galerii

Aby skonfigurować integrację freshworks z usługą Azure AD, należy dodać Freshworks z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Freshworks** w polu wyszukiwania.
1. Wybierz **Freshworks** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla freshworks

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą freshworks przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik pierwszego przysłony działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Freshworks.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą freshworks, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj freshworks SSO](#configure-freshworks-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Freshworks](#create-freshworks-test-user)** — aby mieć odpowiednik B.Simon w Freshworks, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Freshworks** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.freshworks.com/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Freshworks,](mailto:support@freshworks.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aby zmodyfikować opcje **podpisywania** zgodnie z wymaganiami, kliknij przycisk **Edytuj,** aby otworzyć okno dialogowe  **Certyfikat podpisywania SAML.**

     ![image](common/edit-certificate.png)

     ![Konfiguracja Freshworks](./media/freshworks-tutorial/response.png)

    a. Wybierz **opcję Podpisz odpowiedź SAML** jako **opcję podpisywania**.

    b. Kliknij przycisk **Zapisz**.

1. W sekcji **Konfigurowanie freshworks** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Freshworks.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Freshworks**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-freshworks-sso"></a>Konfigurowanie sytów Freshworks

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Freshworks jako administrator i wykonaj następujące czynności:

2. Po lewej stronie menu kliknij ikonę **Zabezpieczenia,** a następnie zaznacz opcję **Logowanie jednokrotne** i wybierz pozycję **SAML SSO** w obszarze **Metody uwierzytelniania**.

    ![Konfiguracja Freshworks](./media/freshworks-tutorial/configure01.png)

3. W sekcji **Logowanie jednokrotne** wykonaj następujące czynności:

    ![Konfiguracja Freshworks](./media/freshworks-tutorial/configure02.png)

    a. Kliknij **przycisk Kopiuj,** aby skopiować **identyfikator jednostki usługodawcy (SP)** dla wystąpienia i wkleić go w polu tekstowym **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. W identyfikatorze **jednostki podanym przez** pole tekstowe IdP wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    d. W polu tekstowym **adresu URL logowania SAML** wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure portal.

    d. Otwórz zakodowany certyfikat Base64 w notatniku, skopiuj jego zawartość i wklej go do pola tekstowego **Certyfikat zabezpieczeń.**

    e. Kliknij przycisk **Zapisz**.

### <a name="create-freshworks-test-user"></a>Utwórz użytkownika testowego Freshworks

W tej sekcji utworzysz użytkownika o nazwie B.Simon w Freshworks. Współpracuj z [zespołem pomocy technicznej Freshworks Client,](mailto:support@freshworks.com) aby dodać użytkowników na platformie Freshworks. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Freshworks w Panelu dostępu należy automatycznie zalogować się do freshworks, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj freshworks z usługą Azure AD](https://aad.portal.azure.com/)

