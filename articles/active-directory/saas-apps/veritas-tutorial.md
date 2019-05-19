---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej.
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
ms.openlocfilehash: 59012bf32a4e1f0532b4d42e510d431180c35730
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865636"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas

W tym samouczku dowiesz się, jak zintegrować Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej z usługą Azure Active Directory (Azure AD).
Integracja logowania jednokrotnego Vault.cloud Enterprise Veritas z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Vault.cloud Enterprise Veritas.
* Użytkownikom można automatycznie zalogowany do Veritas Enterprise Vault.cloud SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Logowanie Jednokrotne Vault.cloud Enterprise VERITAS logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje logowanie Jednokrotne Vault.cloud Enterprise VERITAS **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Dodawanie rejestracji Jednokrotnej Vault.cloud Enterprise Veritas z galerii

Aby skonfigurować integrację Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej w usłudze Azure AD, należy dodać Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej**, wybierz opcję **Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

     ![Logowanie Jednokrotne Vault.cloud Enterprise VERITAS na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w rejestracji Jednokrotnej Vault.cloud Enterprise Veritas musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Veritas Vault.cloud logowania jednokrotnego logowania jednokrotnego dla firm](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego logowania jednokrotnego Vault.cloud Enterprise Veritas](#create-veritas-enterprise-vaultcloud-sso-test-user)**  — aby odpowiednikiem Britta Simon w Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Vault.cloud Enterprise Veritas, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **logowania jednokrotnego Vault.cloud Enterprise Veritas** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Veritas Enterprise Vault.cloud logowania jednokrotnego domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. W **identyfikator** Użyj adresu URL, zgodnie z centrum danych:

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|

    c. W **adres URL odpowiedzi** tekst pola, użyj adresu URL, zgodnie z centrum danych:

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta logowania jednokrotnego Vault.cloud Enterprise Veritas](https://www.veritas.com/support/.html) aby zyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **skonfigurować logowanie Jednokrotne Vault.cloud Enterprise Veritas** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Konfigurowanie Veritas Vault.cloud logowania jednokrotnego logowania jednokrotnego dla firm

Aby skonfigurować logowanie jednokrotne na **Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej** stronie, musisz wysłać pobrany **certyfikat (Base64)** i odpowiednie skopiowany adresy URL z portalu Azure w celu [Veritas Zespół pomocy technicznej Enterprise Vault.cloud Usługa rejestracji Jednokrotnej](https://www.veritas.com/support/.html). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego Vault.cloud Enterprise Veritas.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **logowania jednokrotnego Vault.cloud Enterprise Veritas**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **logowania jednokrotnego Vault.cloud Enterprise Veritas**.

    ![Link Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Tworzenie użytkownika testowego Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej. Praca z [zespołem pomocy technicznej logowania jednokrotnego Vault.cloud Enterprise Veritas](https://www.veritas.com/support/.html) Aby dodać użytkowników na platformie Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Veritas Enterprise Vault.cloud Usługa rejestracji Jednokrotnej w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Veritas SSO Vault.cloud przedsiębiorstwa, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

