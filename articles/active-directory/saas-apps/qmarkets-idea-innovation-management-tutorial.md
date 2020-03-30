---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z Qmarkets Idea & Zarządzanie innowacjami | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a ideą Qmarkets & zarządzanie innowacjami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5ca125c7-f778-4a2d-a573-7cebe1ff634d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3e3f86d761a686993e6ecf32718aa2e15dac92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74536288"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-qmarkets-idea--innovation-management"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z Qmarkets Idea & Zarządzanie innowacjami

W tym samouczku dowiesz się, jak zintegrować Qmarkets Idea & Innovation Management z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Qmarkets Idea & Innovation Management z usługą Azure AD możesz:

* Kontrola w usłudze Azure AD, która ma dostęp do Qmarkets Idea & Innovation Management.
* Umożliwianie użytkownikom automatycznego logowanie się do Qmarkets Idea & zarządzania innowacjami za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Qmarkets Idea & subskrypcja z funkcją rejestracji jednokrotnej (SSO) w ramach programu Innovation Management.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.



* Qmarkets Idea & Innovation Management obsługuje **sp i IDP** zainicjowane SSO
* Qmarkets Idea & Innovation Management obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time


## <a name="adding-qmarkets-idea--innovation-management-from-the-gallery"></a>Dodawanie Qmarkets Idea & Innovation Management z galerii

Aby skonfigurować integrację Qmarkets Idea & Innovation Management z usługą Azure AD, musisz dodać Qmarkets Idea & Innovation Management z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Qmarkets Idea & Innovation Management** w polu wyszukiwania.
1. Wybierz **Qmarkets Idea & Zarządzanie innowacjami** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-qmarkets-idea--innovation-management"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla qmarkets Idea & zarządzania innowacjami

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą Qmarkets Idea & zarządzania innowacjami przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Qmarkets Idea & zarządzania innowacjami.

Aby skonfigurować i przetestować sytów usługi Azure AD za pomocą Qmarkets Idea & Innovation Management, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Qmarkets Idea & zarządzania innowacjami —](#configure-qmarkets-idea--innovation-management-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz Qmarkets Idea & użytkownik testowy zarządzania innowacjami](#create-qmarkets-idea--innovation-management-test-user)** — aby mieć odpowiednik B.Simon w Qmarkets Idea & Innovation Management, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Qmarkets Idea & Innovation Management** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego.**
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<app_url>/sso/saml2/metadata/qmarkets_sp_<endpoint_id>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<app_url>/sso/saml2/acs/qmarkets_sp_<endpoint_id>`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<app_url>/sso/saml2/endpoint/qmarkets_sp_<endpoint_id>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej Qmarkets Idea & Innovation Management Client,](mailto:support@qmarkets.net) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do Qmarkets Idea & Zarządzania innowacjami.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję Qmarkets Idea & Innovation Management**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-qmarkets-idea--innovation-management-sso"></a>Konfigurowanie Qmarkets Idea & Zarządzania Innowacjami SSO

Aby skonfigurować logowanie jednokrotne po stronie **Qmarkets Idea & Innovation Management,** musisz wysłać **adres URL metadanych federacji aplikacji** do zespołu pomocy technicznej [Qmarkets Idea & Innovation Management](mailto:support@qmarkets.net). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-qmarkets-idea--innovation-management-test-user"></a>Tworzenie qmarkets Idea & zarządzania innowacjami użytkownika testu

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Qmarkets Idea & Innovation Management. Qmarkets Idea & Innovation Management obsługuje inicjowanie obsługi administracyjnej użytkowników just-in-time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w Qmarkets Idea & Innovation Management, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Qmarkets Idea & Innovation Management w Panelu dostępu należy automatycznie zalogować się do Qmarkets Idea & Innovation Management, dla którego skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj ideę Qmarkets & zarządzania innowacjami za pomocą usługi Azure AD](https://aad.portal.azure.com/)

