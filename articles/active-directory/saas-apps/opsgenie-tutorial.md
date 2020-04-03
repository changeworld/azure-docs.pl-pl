---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą OpsGenie | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 3bd559b90a4df5573a44b895ce3b097864d1add7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą OpsGenie

W tym samouczku dowiesz się, jak zintegrować OpsGenie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu OpsGenie z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do OpsGenie.
* Włącz użytkownikom automatyczne logowanie do opsGenie za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego OpsGenie.OpsGenie single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* OpsGenie obsługuje zainicjowane przez protokół SSO inicjowane przez **protokół IDP**
* Po skonfigurowaniu OpsGenie można wymusić kontrolę sesji, która chroni eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-opsgenie-from-the-gallery"></a>Dodawanie OpsGenie z galerii

Aby skonfigurować integrację OpsGenie z usługą Azure AD, należy dodać OpsGenie z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **OpsGenie** w polu wyszukiwania.
1. Wybierz **pozycję OpsGenie** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi OpsGenie

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą opsGenie przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby jedno przysłowie jednośrodko-ń działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w OpsGenie.

Aby skonfigurować i przetestować jedno i jednolite usługi Azure AD za pomocą usługi OpsGenie, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj sygosk logowania OpsGenie](#configure-opsgenie-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego OpsGenie](#create-opsgenie-test-user)** — aby mieć odpowiednik B.Simon w OpsGenie, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **OpsGenie** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego adresu URL identyfikatora i odpowiedzi, który został wyjaśniony w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. W sekcji **Konfigurowanie operacji OpsGenie** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do OpsGenie.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **OpsGenie**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-opsgenie-sso"></a>Konfigurowanie sytego zestawu SYC operacji OpsGenie

1. Otwórz inne wystąpienie przeglądarki, a następnie zaloguj się do OpsGenie jako administrator.

2. Kliknij **pozycję Ustawienia**, a następnie kliknij kartę Znak **jednokrotny na.**
   
    ![Logowanie jednokrotne OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Aby włączyć funkcję SSO, wybierz pozycję **Włączone**.
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. W sekcji **Dostawca** kliknij kartę **Usługi Azure Active Directory.**
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na stronie okna dialogowego usługi Azure Active Directory wykonaj następujące kroki:
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Skopiuj wartość **identyfikatora URI aplikacji** i wklej ją do pola tekstowego **Identyfikator (Identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    a. Skopiuj wartość **adresu URL odpowiedzi** i wklej ją do pola tekstowego **Odpowiedz adres URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    a. W polu tekstowym **SAML 2.0 Endpoint** wklej wartość **adresu URL logowania**skopiowaną z witryny Azure portal.
    
    b. W polu **tekstowym Adres url metadanych:** wklej wartość **adresu URL metadanych federacji aplikacji,** która została skopiowana z witryny Azure portal.
    
    d. Aby włączyć logowanie jednokrotne, włącz przełącznik **Włącz logowanie jednokrotne.**

    d. Kliknij **pozycję Zastosuj ustawienia logowania jednośliowe**.

### <a name="create-opsgenie-test-user"></a>Tworzenie użytkownika testowego OpsGenie

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w OpsGenie. 

1. W oknie przeglądarki internetowej zaloguj się do dzierżawy OpsGenie jako administrator.

2. Przejdź do listy Użytkownicy, klikając pozycję **Użytkownicy** w lewym panelu.
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Kliknij **pozycję Dodaj użytkownika**.

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. W polach **tekstowych Poczta e-mail** wpisz adres e-mail B.Simon adresowany w usłudze Azure Active Directory.
   
    b. W polach **tekstowych Imię i nazwisko** wpisz **B.Simon**.
   
    d. Kliknij przycisk **Zapisz**. 

> [!NOTE]
> B.Simon otrzymuje wiadomość e-mail z instrukcjami dotyczącymi konfigurowania profilu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka OpsGenie w Panelu dostępu należy automatycznie zalogować się do opsGenie, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj opsGenie z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)