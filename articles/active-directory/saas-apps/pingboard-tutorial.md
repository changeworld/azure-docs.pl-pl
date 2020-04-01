---
title: 'Samouczek: Integracja usługi Azure Active Directory z pingboardem | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pingboardem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 341d8dd712b858572ec5df76b176258ca87c8857
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094427"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Samouczek: Integracja usługi Azure Active Directory z pingboardem

W tym samouczku dowiesz się, jak zintegrować pingboard z usługą Azure Active Directory (Azure AD).
Integracja pingboard z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do pingboard.
* Można włączyć użytkowników, aby automatycznie zalogować się do pingboard (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pingboard, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego pingboard

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pingboard obsługuje **jednostki** SSO inicjowane przez **SP** i IDP

* Pingboard obsługuje [automatyczne inicjowanie obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Dodawanie pingboard z galerii

Aby skonfigurować integrację pingboard do usługi Azure AD, należy dodać pingboard z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pingboard z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Pingboard**, wybierz **pingboard** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Pingboard na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą pingboard na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w pingboardzie.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą pingboard, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego pingboard](#configure-pingboard-single-sign-on)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego pingboard](#create-pingboard-test-user)** — aby mieć odpowiednik Britta Simon w pingboard, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą pingboard, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **pingboard** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL z listą Pingboard](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL:`http://app.pingboard.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![Informacje o domenie i adresach URL z listą Pingboard](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta pingboard,](https://support.pingboard.com/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie pingboardu** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-pingboard-single-sign-on"></a>Konfigurowanie logowania jednokrotnego pingboard

1. Aby skonfigurować logowanie sytą po stronie pingboard, otwórz nowe okno przeglądarki i zaloguj się na konto pingboard. Aby skonfigurować logowanie jednokrotne, musisz być administratorem pingboardu.

2. W górnym menu wybierz **pozycję Aplikacje > integracje**

    ![Konfigurowanie logowania jednokrotnego](./media/pingboard-tutorial/Pingboard_integration.png)

3. Na stronie **Integracje** znajdź kafelek **"Usługa Azure Active Directory"** i kliknij go.

    ![Integracja logowania jednokrotnego pingboard](./media/pingboard-tutorial/Pingboard_aad.png)

4. W modalnym, który następuje kliknij **"Konfiguruj"**

    ![Przycisk konfiguracji Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. Na następnej stronie można zauważyć, że "Integracja z przyułemkowym platformy Azure jest włączona". Otwórz pobrany plik XML metadanych w notatniku i wklej zawartość **metadanych IDP**.

    ![Ekran konfiguracji SSO pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Plik zostanie zweryfikowany, a jeśli wszystko jest poprawne, logowanie jednokrotne zostanie włączone.

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do pingboard.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **pozycję Pingboard**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Pingboard**.

    ![Łącze Pingboard na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-pingboard-test-user"></a>Tworzenie użytkownika testowego pingboard

Celem tej sekcji jest stworzenie użytkownika o nazwie Britta Simon w Pingboard. Pingboard obsługuje automatyczne inicjowanie obsługi administracyjnej użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](pingboard-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Pingboard jako administrator.

2. Kliknij przycisk **"Dodaj pracownika"** na stronie **Katalog.**

    ![Dodawanie pracownika](./media/pingboard-tutorial/create_testuser_add.png)

3. Na stronie okna dialogowego **"Dodaj pracownika"** wykonaj następujące czynności:

    ![Zapraszanie osób](./media/pingboard-tutorial/create_testuser_name.png)

    a. W polu **tekstowym Imię i nazwisko** wpisz pełną nazwę użytkownika, takiego jak **Britta Simon**.

    b. W polu tekstowym **Wiadomość e-mail** **brittasimon@contoso.com**wpisz adres e-mail użytkownika, taki jak .

    d. W polu **tekstowym Tytuł zadania** wpisz stanowisko Britta Simon.

    d. Z listy rozwijanej **Lokalizacja** wybierz lokalizację Britta Simon.

    e. Kliknij przycisk **Dodaj**.

4. Pojawi się ekran potwierdzenia, aby potwierdzić dodanie użytkownika.

    ![Potwierdzić](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Pingboard w Panelu dostępu należy automatycznie zalogować się do pingboardu, dla którego skonfigurowano logowanie jednośmiętne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie inicjowania obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
