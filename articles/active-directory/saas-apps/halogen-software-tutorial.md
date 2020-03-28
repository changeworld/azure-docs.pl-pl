---
title: 'Samouczek: Integracja usługi Azure Active Directory z saba talentspace | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Saba TalentSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77561367"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z saba talentspace

W tym samouczku dowiesz się, jak zintegrować saba talentspace z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Saba TalentSpace z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do saba talentspace.
* Włącz użytkownikom automatyczne logowanie się do usługi Saba TalentSpace za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego (SSO) saba TalentSpace.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Saba TalentSpace **obsługuje** sp zainicjowane SSO
* Po skonfigurowaniu saba talentspace można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Dodawanie Saba TalentSpace z galerii

Aby skonfigurować integrację saba talentspace do usługi Azure AD, należy dodać Saba TalentSpace z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Saba TalentSpace** w polu wyszukiwania.
1. Wybierz **Saba TalentSpace** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu Saba TalentSpace

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą saba talentspace przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie Saba TalentSpace.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Saba TalentSpace, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne Saba TalentSpace](#configure-saba-talentspace-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Saba TalentSpace](#create-saba-talentspace-test-user)** — aby mieć odpowiednik B.Simon w Saba TalentSpace, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Saba TalentSpace** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://global.hgncloud.com/[companyname]/saml/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://global.hgncloud.com/[companyname]/saml/metadata`

    d. W polu tekstowym **Adres URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** wpisz adres URL, używając następującego wzorca:`https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Saba TalentSpace,](https://support.saba.com/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie saba talentspace** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Saba TalentSpace.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **saba talentspace**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-saba-talentspace-sso"></a>Konfigurowanie sytcha syd w programie Saba TalentSpace

1. W innym oknie przeglądarki zaloguj się do aplikacji **Saba TalentSpace** jako administrator.

2. Kliknij kartę**Options** (Opcje).
  
    ![Co to jest program Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. W okienku nawigacji po lewej stronie kliknij pozycję **SAML Configuration** (Konfiguracja SAML).
  
    ![Co to jest program Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Na stronie **SAML Configuration** (Konfiguracja SAML) wykonaj następujące kroki:

    ![Co to jest program Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. W sekcji **Unique Identifier** (Unikatowy identyfikator) wybierz opcję **NameID** (Id. nazwy).

    b. W sekcji **Unique Identifier Maps To** (Unikatowy identyfikator jest mapowany na) wybierz opcję **Username** (Nazwa użytkownika).
  
    d. Aby przekazać pobrany plik metadanych, kliknij przycisk **Browse** (Przeglądaj), aby wybrać plik, a następnie wybierz przycisk **Upload File** (Przekaż plik).

    d. Aby przetestować konfigurację, kliknij przycisk **Run test** (Uruchom test).

    > [!NOTE]
    > Musisz poczekać na komunikat "*Test SAML został zakończony. Proszę zamknąć to okno*". Następnie zamknij otwarte okno przeglądarki. Pole wyboru **Enable SAML** (Włącz SAML) jest włączone tylko wtedy, gdy ukończono test.

    e. Wybierz pozycję **Enable SAML** (Włącz SAML).

    f. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-saba-talentspace-test-user"></a>Tworzenie użytkownika testowego Saba TalentSpace

Celem tej sekcji jest stworzenie użytkownika o nazwie Britta Simon w Saba TalentSpace.

**Aby utworzyć użytkownika o nazwie Britta Simon w Saba TalentSpace, wykonaj następujące czynności:**

1. Zaloguj się do aplikacji **Saba TalentSpace** jako administrator.

2. Kliknij kartę **User Center** (Centrum użytkowników), a następnie kliknij pozycję **Create User** (Utwórz użytkownika).

    ![Co to jest program Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Na stronie dialogowej **New User** (Nowy użytkownik) wykonaj następujące kroki:

    ![Co to jest program Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. W polu tekstowym **Imię** wpisz imię użytkownika, takiego jak **B**.

    b. W polu **tekstowym Nazwisko** wpisz nazwisko użytkownika, takiego jak **Simon**.

    d. W pola tekstowym **Nazwa użytkownika** wpisz **B.Simon**, nazwę użytkownika, jak w witrynie Azure portal.

    d. W **polach tekstowych Hasło** wpisz hasło do B.Simon.

    e. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Saba TalentSpace w Panelu dostępu należy automatycznie zalogować się do Saba TalentSpace, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj saba talentspace z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)