---
title: 'Samouczek: Integracja usługi Azure Active Directory z bezprzewodowym systemem monitorowania temperatury SensoScientific | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a bezprzewodowym systemem monitorowania temperatury sensoScientific.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091269"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Samouczek: Integracja usługi Azure Active Directory z bezprzewodowym systemem monitorowania temperatury SensoScientific

W tym samouczku dowiesz się, jak zintegrować SensoScientific Wireless Temperature Monitoring System z usługą Azure Active Directory (Azure AD).
Integracja sensoScientific Wireless Temperature Monitoring System z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można sterować, kto ma dostęp do systemu bezprzewodowego monitorowania temperatury sensoScientific.
* Można włączyć użytkowników do automatycznego logowania się do SensoScientific Wireless Temperature Monitoring System (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z systemem bezprzewodowego monitorowania temperatury SensoScientific, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* SensoScientific Wireless Temperature Monitoring System single sign-on enabled subscription SensoScientific Wireless Temperature Monitoring System single sign-on enabled subscription SensoScientific Wireless Temperature Monitoring System single sign-on enabled subscription Senso

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SensoScientific Wireless Temperature Monitoring System obsługuje **IDP** zainicjowane SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Dodawanie czujnikowego bezprzewodowego systemu monitorowania temperatury z galerii

Aby skonfigurować integrację sensoscientific Wireless Temperature Monitoring System do usługi Azure AD, należy dodać SensoScientific Wireless Temperature Monitoring System z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać sensoscientific Wireless Temperature Monitoring System z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SensoScientific Wireless Temperature Monitoring System**, wybierz **SensoScientific Wireless Temperature Monitoring System** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Bezprzewodowy system monitorowania temperatury SensoScientific na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowanie jednokrotne za pomocą SensoScientific Wireless Temperature Monitoring System na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w systemie monitorowania temperatury bezprzewodowej SensoScientific.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą systemu bezprzewodowego monitorowania temperatury SensoScientific, należy wykonać następujące elementy konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj system logowania temperatury bezprzewodowej SensoScientific —](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego sensoScientific Wireless Temperature Monitoring System](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** — aby mieć odpowiednik Britty Simon w sensoScientific Wireless Temperature Monitoring System, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą systemu monitorowania temperatury sieci bezprzewodowej SensoScientific, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **SensoScientific Wireless Temperature Monitoring System** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![SensoScientific Wireless Temperature Monitoring System Domain and URL Url single sign-on information SensoScientific Wireless Temperature Monitoring System Domain and URL Url single sign-on information SensoScientific Wireless Temperature Monitoring System Domain and URLLs single sign-on information Senso](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie systemu bezprzewodowego monitorowania temperatury SensoScientific** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurowanie systemu bezprzewodowego monitorowania temperatury SensoScientific — logowanie jednokrotne

1. Zaloguj się do aplikacji SensoScientific Wireless Temperature Monitoring System jako administrator.

1. W menu nawigacyjnym u góry kliknij polecenie **Konfiguracja** i goto **Konfiguruj** w obszarze **Logowanie jednokrotne,** aby otworzyć ustawienia logowania jednokrotnego i wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Wybierz **pozycję Nazwa wystawcy** jako usługa Azure AD.

    b. W polach tekstowych **adres URL wystawcy** wklej **identyfikator usługi Azure AD** skopiowany z witryny Azure portal.

    d. W **polach tekstowych adresu URL usługi logowania jednokrotnego** wklej **adres URL logowania** skopiowany z witryny Azure portal.

    d. W obszarze **tekstowym adresu URL usługi pojedynczego wylogowania** wklej **adres URL wylogowania** skopiowany z witryny Azure Portal.

    e. Przejrzyj certyfikat pobrany z witryny Azure portal i przekaż tutaj.

    f. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** `brittasimon@yourcompanydomain.extension`użytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do SensoScientific Wireless System monitorowania temperatury.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz opcję **SensoScientific Wireless Temperature Monitoring System**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **SensoScientific Wireless Temperature Monitoring System**.

    ![Łącze SensoScientific Wireless Temperature Monitoring System na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Utwórz użytkownika testu systemu bezprzewodowego monitorowania temperatury SensoScientific

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do sensoScientific Wireless Temperature Monitoring System, muszą być aprowizowani w SensoScientific Wireless System monitorowania temperatury. Współpracuj z  [zespołem wsparcia SensoScientific Wireless Temperature Monitoring System,](https://www.sensoscientific.com/contact-us/)aby dodać użytkowników na platformie SensoScientific Wireless Temperature Monitoring System. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SensoScientific Wireless Temperature Monitoring System w Panelu dostępu należy automatycznie zalogować się do bezprzewodowego systemu monitorowania temperatury SensoScientific, dla którego skonfigurowano logowanie jednośmiętke. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

