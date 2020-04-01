---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem Replicon | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092765"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Samouczek: Integracja programu Replicon z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować program Replicon z usługą Azure Active Directory (Azure AD). Po zintegrowaniu repliconu z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Replicon.
* Włącz użytkownikom automatyczne logowanie do repliconu za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) w replicon.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Replicon **obsługuje** sp zainicjowane SSO.

## <a name="adding-replicon-from-the-gallery"></a>Dodawanie repliconu z galerii

Aby skonfigurować integrację programu Replicon z usługą Azure AD, należy dodać replicon z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Replicon** w polu wyszukiwania.
1. Wybierz **replicon** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą programu Replicon przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w replicon.

Aby skonfigurować i przetestować sytua usługi Azure AD z programem Replicon, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne replicon](#configure-replicon-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Replicon](#create-replicon-test-user)** — aby mieć odpowiednik B.Simon w Replicon, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Replicon** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    1. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://global.replicon.com/!/saml2/<client name>`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Replicon,](https://www.replicon.com/customerzone/contact-support) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Kliknij ikonę edycji/pióra **certyfikatu podpisywania SAML,** aby edytować ustawienia.

    ![Algorytm podpisywania](common/signing-algorithm.png)

    1. Wybierz **pozycję Podpisz asercja SAML** jako **opcję podpisywania**.

    1. Wybierz **SHA-256** jako **algorytm podpisywania**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurowanie wpisu SSO programu Replicon

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Replicon jako administrator.

2. Aby skonfigurować saml 2.0, wykonaj następujące kroki:

    ![Włącz uwierzytelnianie SAML](./media/replicon-tutorial/ic777805.png "Włącz uwierzytelnianie SAML")

    a. Aby wyświetlić okno dialogowe **EnableSAML Authentication2,** dołącz następujące elementy do adresu URL po kluczu firmowym:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Poniżej przedstawiono schemat pełnego adresu URL:`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Kliknij, **+** aby rozwinąć sekcję **konfiguracji w wersji 20.**

   d. Kliknij, **+** aby rozwinąć sekcję **metadataKonfiguracja.**

   d. Wybierz **SHA256** dla xmlSignatureAlgorithm

   e. Kliknij **pozycję Wybierz plik**, aby wybrać plik XML metadanych dostawcy tożsamości, a następnie kliknij przycisk **Prześlij**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Replicon.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Replicon**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-replicon-test-user"></a>Utwórz użytkownika testowego Replicon

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w Replicon.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. W oknie przeglądarki internetowej zaloguj się do witryny firmy Replicon jako administrator.

2. Przejdź do **użytkownika administracji \> **.

    ![Użytkownicy](./media/replicon-tutorial/ic777806.png "Użytkownicy")

3. Kliknij **+Dodaj użytkownika**.

    ![Dodaj użytkownika](./media/replicon-tutorial/ic777807.png "Dodaj użytkownika")

4. W sekcji **Profil użytkownika** wykonaj następujące czynności:

    ![Profil użytkownika](./media/replicon-tutorial/ic777808.png "Profil użytkownika")

    a. W polu tekstowym **Nazwa logowania** wpisz adres e-mail usługi Azure `B.Simon@contoso.com`AD użytkownika usługi Azure AD, który chcesz aprowizować, tak jak .

    > [!NOTE]
    > Nazwa logowania musi być zgodna z adresem e-mail użytkownika w usłudze Azure AD

    b. Jako **typ uwierzytelniania**wybierz opcję **SSO**.

    d. Ustaw identyfikator uwierzytelniania na taką samą wartość jak nazwa logowania (adres e-mail usługi Azure AD użytkownika)

    d. W polach tekstowych **Dział** wpisz dział użytkownika.

    e. Jako **typ pracownika**wybierz pozycję **Administrator**.

    f. Kliknij **pozycję Zapisz profil użytkownika**.

> [!NOTE]
> Można użyć innych narzędzi do tworzenia konta użytkownika Replicon lub interfejsów API dostarczonych przez replicon do aprowizowania kont użytkowników usługi Azure AD.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Replicon w Panelu dostępu należy automatycznie zalogować się do repliconu, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)