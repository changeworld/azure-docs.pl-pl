---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Jamf Pro | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b575b74c80499f2ddd6648bf051b5245077d2f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58906144"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Samouczek: Samouczek: integracja usługi Azure Active Directory z oprogramowaniem Jamf Pro

Z tego samouczka dowiesz się, jak zintegrować oprogramowanie Jamf Pro z usługą Azure Active Directory (Azure AD).
Zintegrowanie oprogramowania Jamf Pro z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do oprogramowania Jamf Pro.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do oprogramowania Jamf Pro (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem Jamf Pro, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja oprogramowania Jamf Pro z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie Jamf Pro obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**

## <a name="adding-jamf-pro-from-the-gallery"></a>Dodawanie oprogramowania Jamf Pro z galerii

Aby skonfigurować integrację oprogramowania Jamf Pro z usługą Azure AD, musisz dodać oprogramowanie Jamf Pro z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie Jamf Pro z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Jamf Pro**, wybierz pozycję **Jamf Pro** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Jamf Pro na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z oprogramowaniem Jamf Pro, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu Jamf Pro.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z oprogramowaniem Jamf Pro, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w oprogramowaniu Jamf Pro](#configure-jamf-pro-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego oprogramowania Jamf Pro](#create-jamf-pro-test-user)** — aby mieć w oprogramowaniu Jamf Pro odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w oprogramowaniu Jamf Pro, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Jamf Pro** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz przeprowadzić konfigurację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL aplikacji Jamf Pro dla logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.jamfcloud.com/saml/SSO`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.jamfcloud.com`

    ![Informacje o domenie i adresach URL aplikacji Jamf Pro dla logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Rzeczywista wartość identyfikatora zostanie pobrana z sekcji **Single Sign-On** (Logowanie jednokrotne) w portalu oprogramowania Jamf Pro, co objaśniono w dalszej części tego samouczka. Możesz wyodrębnić rzeczywistą wartość **poddomeny** z wartości identyfikatora i użyć tych informacji o **poddomenie** w adresie URL logowania i adresie URL odpowiedzi. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w oprogramowaniu Jamf Pro

1. Aby zautomatyzować konfigurację w oprogramowaniu Jamf Pro, musisz zainstalować **rozszerzenie przeglądarki do bezpiecznego logowania Moje aplikacje**, klikając pozycję **Zainstaluj rozszerzenie**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj aplikację Jamf Pro**. Spowoduje to bezpośrednie przejście do aplikacji Jamf Pro. W tym miejscu podaj poświadczenia administratora w celu zalogowania się do aplikacji Jamf Pro. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. Jeśli chcesz ręcznie skonfigurować oprogramowanie Jamf Pro, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmowej Jamf Pro jako administrator, a następnie wykonaj następujące czynności:

4. Kliknij **ikonę Ustawienia** w prawym górnym rogu strony.

    ![Konfiguracja oprogramowania Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Kliknij pozycję **Single Sign On** (Logowanie jednokrotne).

    ![Konfiguracja oprogramowania Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na stronie **Single Sign On**(Logowanie jednokrotne) wykonaj następujące czynności:

    ![Logowanie jednokrotne w oprogramowaniu Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Wybierz pozycję **Jamf Pro Server** (Serwer oprogramowania Jamf Pro), aby włączyć dostęp przy użyciu logowania jednokrotnego.

    b. Wybranie opcji **Allow bypass for all users** (Zezwalaj na obejście dla wszystkich użytkowników) spowoduje, że użytkownicy nie będą przekierowywani do strony logowania dostawcy tożsamości w celu uwierzytelniania, ale zamiast tego będą mogli bezpośrednio zalogować się do oprogramowania Jamf Pro. Gdy użytkownik spróbuje uzyskać dostęp do oprogramowania Jamf Pro za pośrednictwem dostawcy tożsamości, nastąpi uwierzytelnianie i autoryzacja za pomocą logowania jednokrotnego zainicjowanego przez dostawcę tożsamości.

    c. Wybierz opcję **NameID** dla ustawienia **USER MAPPING: SAML** (ODWZOROWANIE UŻYTKOWNIKA: SAML). Domyślnie to ustawienie ma wartość **NameID**, ale można zdefiniować atrybut niestandardowy.

    d. Wybierz opcję **Email** dla ustawienia **USER MAPPING: JAMF PRO** (ODWZOROWANIE UŻYTKOWNIKA: JAMF PRO). Oprogramowanie Jamf Pro mapuje atrybuty SAML wysyłane przez dostawcę tożsamości na następujące sposoby: według użytkowników i według grup. Gdy użytkownik próbuje uzyskać dostęp do oprogramowania Jamf Pro, domyślnie oprogramowanie Jamf Pro pobiera informacje o użytkowniku od dostawcy tożsamości i uzgadnia je względem kont użytkowników w oprogramowaniu Jamf Pro. Jeśli przychodzące konto użytkownika nie istnieje w oprogramowaniu Jamf Pro, wykonywane jest dopasowanie nazwy grupy.

    e. Wklej wartość `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` w polu tekstowym **GROUP ATTRIBUTE NAME** (NAZWA ATRYBUTU GRUPY).

7. Na tej samej stronie przewiń ekran do pola **IDENTITY PROVIDER** (DOSTAWCA TOŻSAMOŚCI) w sekcji **Single Sign-On** (Logowanie jednokrotne), a następnie wykonaj następujące kroki:

    ![Konfiguracja oprogramowania Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Wybierz **innych** jako opcję z **dostawcy tożsamości** listy rozwijanej.

    b. W polu tekstowym **OTHER PROVIDER** (INNY DOSTAWCA) wprowadź wartość **Azure AD**.

    c. Wybierz **adres URL metadanych** jako opcję z **źródła METADANYCH dostawcy tożsamości** listy rozwijanej i Wklej w poniższym polu tekstowym **adres Url metadanych Federacji aplikacji** wartość który skopiowano z witryny Azure portal.

    d. Skopiuj wartość pola **Identyfikator jednostki** i wklej ją w polu tekstowym **Identyfikator (identyfikator jednostki)** w sekcji **Domena i adresy URL oprogramowania Jamf Pro** w witrynie Azure Portal.

    > [!NOTE]
    > W tym miejscu rozmyta wartość jest częścią poddomeny. Ta wartość służy do wypełnienia adresu URL logowania i adresu URL odpowiedzi w sekcji **Domena i adresy URL oprogramowania Jamf Pro** w witrynie Azure Portal.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Jamf Pro.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Jamf Pro**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Jamf Pro**.

    ![Link oprogramowania Jamf Pro na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-jamf-pro-test-user"></a>Tworzenie użytkownika testowego oprogramowania Jamf Pro

Aby umożliwić użytkownikom usługi Azure AD logowanie się w oprogramowaniu Jamf Pro, muszą być oni aprowizowaniu w oprogramowaniu Jamf Pro. W przypadku oprogramowania Jamf Pro aprowizacja jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmowej oprogramowania Jamf Pro jako administrator.

2. Kliknij **ikonę Ustawienia** w prawym górnym rogu strony.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Kliknij pozycję **Konta i grupy użytkowników oprogramowania Jamf Pro**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user1.png)

4. Kliknij przycisk **Nowy**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user2.png)

5. Wybierz pozycję **Utwórz standardowe konto**.

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user3.png)

6. W oknie dialogowym **Nowe konto** wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/jamfprosamlconnector-tutorial/user4.png)

    a. W polu tekstowym **USERNAME** (NAZWA UŻYTKOWNIKA) wpisz pełną nazwę użytkownika BrittaSimon.

    b. Wybierz odpowiednie opcje zgodnie z Twoją organizacją dla pól **ACCESS LEVEL** (POZIOM DOSTĘPU), **PRIVILEGE SET** (ZESTAW UPRAWNIEŃ) i **ACCESS STATUS** (STAN DOSTĘPU).

    c. W polu tekstowym **FULL NAME** (IMIĘ I NAZWISKO) wpisz imię i nazwisko Britta Simon.

    d. W polu tekstowym **EMAIL ADDRESS** (ADRES E-MAIL) wpisz adres e-mail konta użytkownika Britta Simon.

    e. W polu tekstowym **PASSWORD** (HASŁO) wpisz hasło użytkownika.

    f. W polu tekstowym **VERIFY PASSWORD** (WERYFIKUJ HASŁO) wpisz hasło użytkownika.

    g. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Jamf Pro w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Jamf Pro, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
