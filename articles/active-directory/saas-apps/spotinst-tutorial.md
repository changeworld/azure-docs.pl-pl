---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Spotinst | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b3c9a0152144ac0d705c7d3f12c18cbc38398f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090145"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Spotinst

W tym samouczku dowiesz się, jak zintegrować Spotinst w usłudze Azure Active Directory (Azure AD).
Integrowanie Spotinst z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Spotinst.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Spotinst (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Spotinst, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Spotinst logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Spotinst **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-spotinst-from-the-gallery"></a>Dodawanie Spotinst z galerii

Aby skonfigurować integrację Spotinst w usłudze Azure AD, należy dodać Spotinst z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Spotinst z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Spotinst**, wybierz opcję **Spotinst** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Spotinst na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Spotinst w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Spotinst musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Spotinst, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Spotinst logowania jednokrotnego](#configure-spotinst-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Spotinst](#create-spotinst-test-user)**  — aby odpowiednikiem Britta Simon w Spotinst połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Spotinst, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Spotinst** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Spotinst domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-preintegrated-relay.png)

    a. Sprawdź **Ustaw dodatkowe adresy URL**.

    b. W **tan przekaźnika** pole tekstowe, wpisz wartość: `<ID>`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Spotinst domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://console.spotinst.com`

    > [!NOTE]
    > Wartość stanu przekazywania nie jest prawdziwe. Wartość stanu przekazywania zostaną zaktualizowane o wartość tan przekaźnika rzeczywistą zostało wyjaśnione w dalszej części tego samouczka.

6. Aplikacja Spotinst oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Name (Nazwa) | Atrybut źródłowy|
    | -----| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

9. Na **Konfigurowanie Spotinst** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-spotinst-single-sign-on"></a>Konfigurowanie Spotinst logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się do Spotinst jako Administrator zabezpieczeń.

2. Kliknij pozycję **ikony użytkownika** w prawej górnej części ekranu i kliknij przycisk **ustawienia**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Kliknij pozycję **zabezpieczeń** u góry, a następnie wybierz pozycję **dostawców tożsamości** i wykonaj następujące czynności:

    ![Spotinst zabezpieczeń](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopiuj **tan przekaźnika** wartości dla swojego wystąpienia, a następnie wklej je **tan przekaźnika** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **PRZEGLĄDAJ** można przekazać pliku xml metadanych, który został pobrany z witryny Azure portal

    c. Kliknij przycisk **SAVE** (Zapisz).

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Spotinst.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Spotinst**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Spotinst**.

    ![Link Spotinst na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-spotinst-test-user"></a>Tworzenie użytkownika testowego Spotinst

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Spotinst.

1. Po skonfigurowaniu aplikacji w **SP** zainicjowano tryb, wykonaj następujące czynności:

   a. W oknie przeglądarki internetowej innej Zaloguj się do Spotinst jako Administrator zabezpieczeń.

   b. Kliknij pozycję **ikony użytkownika** w prawej górnej części ekranu i kliknij przycisk **ustawienia**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Kliknij przycisk **użytkowników** i wybierz **Dodaj użytkownika**.

    ![Ustawienia Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. W sekcji Dodaj użytkownika wykonaj następujące czynności:

    ![Ustawienia Spotinst](./media/spotinst-tutorial/adduser2.png)

    * W **imię i nazwisko** polu tekstowym Wprowadź pełną nazwę użytkownika, takich jak **BrittaSimon**.

    * W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak `brittasimon\@contoso.com`.

    * Wybierz swoje szczegóły specyficzne dla organizacji **kont, roli konta i roli organizacji**.

2. Po skonfigurowaniu aplikacji w **tożsamości** zainicjowano tryb, miejsca jest nie czynność do wykonania dla Ciebie w tej sekcji. Spotinst obsługę just-in-time, który jest domyślnie włączona. Nowy użytkownik jest tworzony podczas próby dostępu Spotinst, jeśli go jeszcze nie istnieje.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Spotinst w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Spotinst, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

