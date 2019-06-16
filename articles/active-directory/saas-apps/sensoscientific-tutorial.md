---
title: 'Samouczek: Integracja usługi Azure Active Directory systemie SensoScientific bezprzewodowej temperatury monitorowania | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między SensoScientific bezprzewodowej temperatury monitorowania systemu i usługi Azure Active Directory.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091269"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Samouczek: Integracja usługi Azure Active Directory SensoScientific bezprzewodowej temperatury monitorowania systemu

W tym samouczku dowiesz się, jak zintegrować SensoScientific bezprzewodowej temperatury monitorowania systemu za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie systemu monitorowania temperatury SensoScientific bezprzewodowej z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do systemu monitorowania temperatury SensoScientific sieci bezprzewodowej.
* Użytkownikom można automatycznie zalogowany do SensoScientific bezprzewodowej temperatury monitorowanie systemu (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD przy użyciu SensoScientific bezprzewodowej temperatury monitorowania systemu, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* System monitorowania SensoScientific bezprzewodowej temperatury logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje System monitorowania SensoScientific bezprzewodowej temperatury **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Dodawanie SensoScientific bezprzewodowej temperatury monitorowania systemu z galerii

Aby skonfigurować integrację SensoScientific bezprzewodowej temperatury monitorowania systemu w usłudze Azure AD, należy dodać SensoScientific bezprzewodowej temperatury monitorowania systemu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SensoScientific bezprzewodowej temperatury monitorowania systemu z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SensoScientific bezprzewodowej temperatury monitorowania systemu**, wybierz opcję **SensoScientific bezprzewodowej temperatury monitorowania systemu** z panelu wynik kliknięcie **Dodaj**  przycisk, aby dodać aplikację.

    ![SensoScientific bezprzewodowej temperatury systemu monitorowania na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą SensoScientific bezprzewodowej temperatury monitorowania systemu w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika SensoScientific bezprzewodowej temperatury monitorowania systemu musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu SensoScientific bezprzewodowej temperatury monitorowania systemu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie SensoScientific bezprzewodowej temperatury monitorowania systemu logowania jednokrotnego](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego systemu monitorowania SensoScientific bezprzewodowej temperatury](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)**  — aby odpowiednikiem Britta Simon w SensoScientific bezprzewodowej temperatury monitorowania systemu, który jest połączony z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu SensoScientific bezprzewodowej temperatury monitorowania systemu, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **SensoScientific bezprzewodowej temperatury monitorowania systemu** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![System monitorowania programu SensoScientific bezprzewodowej temperatury domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **SensoScientific bezprzewodowej temperatury monitorowania systemu** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurowanie temperatury bezprzewodowej SensoScientific monitorowania systemu logowania jednokrotnego

1. Zaloguj się do aplikacji systemu monitorowania SensoScientific bezprzewodowej temperatury jako administrator.

1. W menu nawigacji u góry kliknij **konfiguracji** i przejdź do **Konfiguruj** w obszarze **Single Sign On** otworzyć pojedynczy znak na ustawienia i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Wybierz **Nazwa wystawcy** jako usługi Azure AD.

    b. W **adres URL wystawcy** pola tekstowego, Wklej **usługi Azure AD identyfikator** skopiowanej w witrynie Azure portal.

    c. W **pojedynczy znak na adres URL usługi** pola tekstowego, Wklej **adres URL logowania** skopiowanej w witrynie Azure portal.

    d. W **adresu URL usługi wylogowania jednokrotnego** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    e. Przeglądaj certyfikatu, który został pobrany z witryny Azure portal i przekaż go tutaj.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do systemu monitorowania temperatury SensoScientific sieci bezprzewodowej.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **SensoScientific bezprzewodowej temperatury monitorowania systemu**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **SensoScientific bezprzewodowej temperatury monitorowania systemu**.

    ![Łącze SensoScientific bezprzewodowej temperatury monitorowania systemu, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Tworzenie użytkownika testowego systemu monitorowania temperatury SensoScientific sieci bezprzewodowej

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do systemu monitorowania temperatury SensoScientific sieci bezprzewodowej, musi być obsługiwana SensoScientific bezprzewodowej temperatury monitorowania systemu. Praca z [zespołem pomocy technicznej systemu monitorowania SensoScientific bezprzewodowej temperatury](https://www.sensoscientific.com/contact-us/) Aby dodać użytkowników na platformie systemu monitorowania temperatury SensoScientific sieci bezprzewodowej. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SensoScientific bezprzewodowej temperatury monitorowania systemu, w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze SensoScientific bezprzewodowej temperatury monitorowania systemu dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

