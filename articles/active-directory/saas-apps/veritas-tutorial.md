---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją SSO veritas Enterprise Vault.cloud | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logiem samoso w chmurze Veritas Enterprise Vault.cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 43094cabab3cfc93e0dffa59a15867d01b036d38
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087617"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Samouczek: Integracja usługi Azure Active Directory z identyfikatorem RSO veritas enterprise vault.cloud

W tym samouczku dowiesz się, jak zintegrować usługę Veritas Enterprise Vault.cloud SSO z usługą Azure Active Directory (Azure AD).
Integracja usługi Veritas Enterprise Vault.cloud SSO z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do usługi Veritas Enterprise Vault.cloud SSO.
* Można włączyć automatyczne logowanie użytkowników do logowania jednokrotnego usługi Veritas Enterprise Vault.cloud (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją SSO veritas Enterprise Vault.cloud, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja pojedynczego logowania jednokrotnego veritas Enterprise Vault.cloud

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Veritas Enterprise Vault.cloud SSO obsługuje sygo sygnicowe zainicjowane przez **SP**

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Dodawanie aplikacji SSO Veritas Enterprise Vault.cloud z galerii

Aby skonfigurować integrację usługi Veritas Enterprise Vault.cloud SSO z usługą Azure AD, należy dodać wpis SSO Veritas Enterprise Vault.cloud z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać identyfikator sytowy Veritas Enterprise Vault.cloud z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **veritas Enterprise Vault.cloud SSO**, wybierz **veritas Enterprise Vault.cloud SSO** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Veritas Enterprise Vault.cloud SSO na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą Veritas Enterprise Vault.cloud SSO na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie Samoso veritas Enterprise Vault.cloud.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego usługi Veritas Enterprise Vault.cloud, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj jednokrotne logowanie logowania jednokrotnego usługi Veritas Enterprise Vault.cloud](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SSO usługi Veritas Enterprise Vault.cloud](#create-veritas-enterprise-vaultcloud-sso-test-user)** — aby mieć odpowiednik użytkownika Britta Simon w aplikacji SSO Veritas Enterprise Vault.cloud, która jest połączona z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego usługi Veritas Enterprise Vault.cloud, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **logowania jednokrotnego veritas Enterprise Vault.cloud** wybierz pozycję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Veritas Enterprise Vault.cloud — informacje o logach logowania jednokrotnego i adresów URL](common/sp-identifier-reply.png)

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. W polu **Identyfikator** użyj adresu URL zgodnie z centrum danych:

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|

    d. W polu tekstowym **Odpowiedz na adres URL** użyj adresu URL zgodnie z centrum danych:

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej klienta SSO veritas Enterprise Vault.cloud.](https://www.veritas.com/support/.html) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie identyfikatorów SSO Veritas Enterprise Vault.cloud** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Konfigurowanie logowania jednokrotnego logowania jednokrotnego usługi Veritas Enterprise Vault.cloud

Aby skonfigurować logowanie jednokrotne po stronie **logowania jednokrotnego veritas Enterprise Vault.cloud,** należy wysłać pobrany **certyfikat (Base64)** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej logowania jednokrotnego Veritas Enterprise Vault.cloud](https://www.veritas.com/support/.html). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do veritas Enterprise Vault.cloud Logowania.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pozycję **Veritas Enterprise Vault.cloud .**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Veritas Enterprise Vault.cloud SSO**.

    ![Łącze SSO veritas Enterprise Vault.cloud na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Tworzenie użytkownika testowego SSO programu Veritas Enterprise Vault.cloud

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Veritas Enterprise Vault.cloud SSO. Współpracuj z [zespołem pomocy technicznej veritas Enterprise Vault.cloud SSO,](https://www.veritas.com/support/.html) aby dodać użytkowników na platformie SSO Veritas Enterprise Vault.cloud. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka logującego się logującą logującą usługę Veritas Enterprise Vault.cloud w Panelu dostępu należy automatycznie zalogować się do logaJm jednośmigłowego Veritas Enterprise Vault.cloud, dla którego skonfigurowano logującą jednoślik.You should be automatically signed in to the Veritas Enterprise Vault.cloud SSO for which you set up LogsO. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

