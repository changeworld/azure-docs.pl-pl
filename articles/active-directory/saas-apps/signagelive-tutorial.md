---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Signagelive | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160928"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Signagelive

Z tego samouczka dowiesz się, jak zintegrować platformę Signagelive z usługą Azure Active Directory (Azure AD).
Integrowanie platformy Signagelive z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do platformy Signagelive.
* Można włączyć użytkowników, aby automatycznie zalogować się do Signagelive (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą Signagelive, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [miesięczną wersję próbną.](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja signagelive z funkcją logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Signagelive obsługuje logowanie SSO inicjowane przez sp.

## <a name="add-signagelive-from-the-gallery"></a>Dodaj Signagelive z galerii

Aby skonfigurować integrację signagelive z usługą Azure AD, należy najpierw dodać signagelive z galerii do listy zarządzanych aplikacji SaaS.

Aby dodać signagelive z galerii, należy wykonać następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz ikonę **usługi Azure Active Directory.**

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **strony Aplikacje dla przedsiębiorstw,** a następnie wybierz opcję **Wszystkie aplikacje.**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Signagelive**. 

     ![Signagelive na liście wyników](common/search-new-app.png)

5. Z okienka wyników wybierz **pozycję Signagelive,** a następnie wybierz przycisk **Dodaj,** aby dodać aplikację.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z platformą Signagelive, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić łącze między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Signagelive.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi Signagelive, najpierw wykonaj następujące bloki konstrukcyjne:

1. [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Skonfiguruj logowanie jednokrotne Signagelive,](#configure-signagelive-single-sign-on) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Utwórz użytkownika testowego Signagelive,](#create-a-signagelive-test-user) aby mieć odpowiednik Britta Simon w Signagelive, który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi Signagelive, należy wykonać następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Signagelive** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz pozycję **SAML,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML** należy wykonać następujące kroki:

    ![Domena i adresy URL platformy Signagelive — informacje dotyczące logowania jednokrotnego](common/sp-signonurl.png)

    W polu **Adres URL logowania** wprowadź adres URL, który używa następującego wzorca:`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego adresu URL logowania. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej klienta Signagelive](mailto:support@signagelive.com) . Można również odwołać się do wzorców, które są wyświetlane w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Saml Podpisywanie certyfikatów** wybierz pozycję **Pobierz,** aby pobrać **certyfikat (raw)** z podanych opcji zgodnie z wymaganiami. Następnie zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfigurowanie funkcji Signagelive** skopiuj potrzebne adresy URL.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-signagelive-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w ujmowanie na żywo

Aby skonfigurować logowanie jednokrotne po stronie Signagelive, wyślij pobrany **certyfikat (raw)** i skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej Signagelive](mailto:support@signagelive.com). Zapewniają one, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** należy wykonać następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź pole **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** brittasimon@yourcompanydomain.extensionwpisz " ". Na przykład w takim przypadku możnaBrittaSimon@contoso.comwprowadzić " ".

    d. Zaznacz pole wyboru **Pokaż hasło,** a następnie zanotuj wartość wyświetlaną w polu Hasło.

    d. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do platformy Signagelive.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Signagelive**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Signagelive**.

    ![Link Signagelive na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika.** Następnie w oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** na liście **Użytkownicy** wybierz pozycję **Britta Simon**. Następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę,** wybierz odpowiednią rolę dla użytkownika z listy. Następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przydziału** wybierz przycisk **Przypisz.**

### <a name="create-a-signagelive-test-user"></a>Tworzenie użytkownika testowego Signagelive

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na platformie Signagelive. Współpracuj z [zespołem pomocy technicznej Signagelive,](mailto:support@signagelive.com) aby dodać użytkowników na platformie Signagelive. Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu MyApps.

Po wybraniu kafelka **Signagelive** w portalu MyApps powinieneś zostać automatycznie zalogowany. Aby uzyskać więcej informacji o portalu MyApps, zobacz [Co to jest portal MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

