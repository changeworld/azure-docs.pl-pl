---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z zestawem narzędzi SAML usługi Azure AD | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a zestawem narzędzi SAML usługi Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7902112c1694bacfeb45b5f20db80d5136642169
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047951"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z zestawem narzędzi SAML usługi Azure AD

W tym samouczku dowiesz się, jak zintegrować zestaw narzędzi Saml usługi Azure AD z usługą Azure Active Directory (Azure AD). Po zintegrowaniu zestawu narzędzi SAML usługi Azure AD z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do zestawu narzędzi SAML usługi Azure AD.
* Włącz użytkownikom automatyczne logowanie się do zestawu narzędzi Saml usługi Azure AD za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) usługi Azure AD SAML Toolkit.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Zestaw narzędzi Saml usługi Azure AD obsługuje sso inicjowane przez usługę **SP**
* Po skonfigurowaniu zestawu narzędzi Saml usługi Azure AD można wymusić kontrolę sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Dodawanie zestawu narzędzi SAML usługi Azure z galerii

Aby skonfigurować integrację zestawu narzędzi Saml usługi Azure AD do usługi Azure AD, należy dodać zestaw narzędzi Saml usługi Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **zestaw narzędzi Saml usługi Azure AD** w polu wyszukiwania.
1. Wybierz **zestaw narzędzi SAML usługi Azure AD z** panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla zestawu narzędzi SAML usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą zestawu narzędzi Saml usługi Azure AD przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w zestawie narzędzi SAML usługi Azure AD.

Aby skonfigurować i przetestować przystawkę SSO usługi Azure AD za pomocą zestawu narzędzi SAML usługi Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie logowania do zestawu narzędzi azure SAML](#configure-azure-ad-saml-toolkit-sso)** usługi Azure SAML — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego zestawu narzędzi Azure AD SAML](#create-azure-ad-saml-toolkit-test-user)** — aby mieć odpowiednik B.Simon w zestawie narzędzi SAML usługi Azure AD, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Saml usługi Azure AD SAML** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://samltoolkit.azurewebsites.net/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://samltoolkit.azurewebsites.net`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (raw)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie zestawu narzędzi SAML usługi Azure AD,** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do zestawu narzędzi SAML usługi Azure AD.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Azure AD SAML Toolkit**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurowanie sytuowania zestawu narzędzi saml usługi Azure AD

1. Otwórz nowe okno przeglądarki sieci Web, jeśli nie zarejestrowałeś się w witrynie azure AD SAML Toolkit, najpierw zarejestruj się, klikając **przycisk Zarejestruj**. Jeśli już się zarejestrowałeś, zaloguj się do witryny firmy Azure AD SAML Toolkit przy użyciu zarejestrowanych poświadczeń logowania.

    ![Rejestr zestawu narzędzi SAML usługi Azure](./media/saml-toolkit-tutorial/register.png)

1. Kliknij **konfigurację SAML**.

    ![Konfiguracja saml zestawu narzędzi usługi Azure AD SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Kliknij przycisk **Utwórz**.

    ![Tworzenie zestawu narzędzi SAML usługi Azure AD](./media/saml-toolkit-tutorial/createsso.png)

1. Na stronie **Konfiguracja SSO SAML** wykonaj następujące czynności:

    ![Tworzenie zestawu narzędzi SAML usługi Azure AD](./media/saml-toolkit-tutorial/fill-details.png)

    1. W polu tekstowym **Adres URL logowania** wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.

    1. W polu tekstowym **identyfikatora usługi Azure AD** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    1. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    1. Kliknij **pozycję Wybierz plik** i przekaż plik **certyfikatu (raw),** który został pobrany z witryny Azure portal.

    1. Kliknij przycisk **Utwórz**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Tworzenie użytkownika testowego zestawu narzędzi SAML usługi Azure

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w usłudze Azure AD SAML Toolkit. Zestaw narzędzi Saml usługi Azure AD obsługuje inicjowanie obsługi administracyjnej tylko w czasie, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w zestawie narzędzi SAML usługi Azure AD, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka zestawu narzędzi SAML usługi Azure AD w Panelu programu Access należy automatycznie zalogować się do zestawu narzędzi SAML usługi Azure AD, dla którego skonfigurowano logującą się do rejestru jednośle. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj zestaw narzędzi SAML usługi Azure za pomocą usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić zestaw narzędzi SAML usługi Azure ad za pomocą zaawansowanej widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/protect-azure)