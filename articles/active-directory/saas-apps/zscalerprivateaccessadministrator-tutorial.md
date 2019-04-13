---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu rozwiązania Zscaler prywatny dostęp administratora | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i rozwiązania Zscaler prywatnej dostępu administratora.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ef5fa478eb171dc33ef2874469bc353a8561619
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546951"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Samouczek: Integracja usługi Azure Active Directory Administrator dostępu prywatnego rozwiązania Zscaler

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler prywatny dostęp administratora z usługi Azure Active Directory (Azure AD).
Integracja rozwiązania Zscaler prywatny dostęp administratora z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler prywatnej dostępu administratora.
* Użytkownikom można automatycznie zalogowany do rozwiązania Zscaler prywatny dostęp administratora (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu rozwiązania Zscaler prywatnej dostępu administratora, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Administrator dostępu prywatnego rozwiązania Zscaler logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Administrator dostępu prywatnego rozwiązania Zscaler obsługuje **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Dodawanie administratora dostępu prywatnego rozwiązania Zscaler z galerii

Aby skonfigurować integrację Administrator dostępu rozwiązania Zscaler prywatne w usłudze Azure AD, należy dodać administratora dostępu prywatnego rozwiązania Zscaler z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać administratora dostępu prywatnego rozwiązania Zscaler z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Administrator dostępu prywatnego rozwiązania Zscaler**, wybierz opcję **Administrator dostępu prywatnego rozwiązania Zscaler** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Administrator dostępu prywatnego rozwiązania Zscaler na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler prywatne Administrator dostępu w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Administrator dostępu prywatnego rozwiązania Zscaler musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu rozwiązania Zscaler prywatnej dostępu administratora, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie rozwiązania Zscaler prywatny dostęp administratora logowanie jednokrotne](#configure-zscaler-private-access-administrator-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Administrator dostępu prywatnego rozwiązania Zscaler](#create-zscaler-private-access-administrator-test-user)**  — aby odpowiednikiem Britta Simon w prywatnej rozwiązania Zscaler Administrator dostępu połączoną usługę Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu rozwiązania Zscaler prywatnej dostępu administratora, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Administrator dostępu prywatnego rozwiązania Zscaler** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Administrator dostępu prywatnego rozwiązania Zscaler domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-relay.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `idpadminsso`

5.  Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, wykonaj następujące kroki:

    ![Administrator dostępu prywatnego rozwiązania Zscaler domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/both-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Administrator dostępu prywatnego rozwiązania Zscaler](https://help.zscaler.com/zpa-submit-ticket) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. Na **Ustaw się Administrator dostępu prywatnego rozwiązania Zscaler** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfiguruj Logowanie administratora dostępu prywatnego jednego rozwiązania Zscaler

1. W oknie przeglądarki internetowej innej Zaloguj się do rozwiązania Zscaler prywatnej dostępu administratora jako Administrator.

2. W górnej części, kliknij przycisk **administracji** i przejdź do **uwierzytelniania** kliknij sekcję **konfigurację dostawcy tożsamości**.

    ![Administrator dostępu prywatnego rozwiązania Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. W prawym górnym rogu, kliknij przycisk **Dodawanie konfiguracji dostawcy tożsamości**. 

    ![Administrator dostępu prywatnego rozwiązania Zscaler Addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na **Dodawanie konfiguracji dostawcy tożsamości** strony należy wykonać następujące czynności:
 
    ![Administrator dostępu prywatnego rozwiązania Zscaler Idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kliknij przycisk **wybierz plik** do przekazania pobrany plik metadanych z usługi Azure AD w **przekazywania plików metadanych dostawcy tożsamości** pola.

    b. Odczytuje **metadanych tożsamości** z usługi Azure AD i wypełni wszystkie informacje dotyczące pól, jak pokazano poniżej.

    ![Administrator dostępu prywatnego rozwiązania Zscaler Idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Wybierz **logowanie jednokrotne** jako **administratora**.

    d. Wybierz domenę z **domen** pola.
    
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
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do rozwiązania Zscaler prywatny dostęp administratora.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Administrator dostępu prywatnego rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Administrator dostępu prywatnego rozwiązania Zscaler**.

    ![Administrator dostępu prywatnego rozwiązania Zscaler łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Tworzenie użytkownika testowego Administrator dostępu prywatnego rozwiązania Zscaler

Aby umożliwić użytkownikom usługi Azure AD zalogować się do rozwiązania Zscaler prywatnej dostępu administratora, są aprowizowane do rozwiązania Zscaler prywatnej dostępu administratora. W przypadku rozwiązania Zscaler prywatnej dostępu administratora, inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy rozwiązania Zscaler prywatnej dostępu administratora jako administrator.

2. W górnej części, kliknij przycisk **administracji** i przejdź do **uwierzytelniania** kliknij sekcję **konfigurację dostawcy tożsamości**.

    ![Administrator dostępu prywatnego rozwiązania Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Kliknij przycisk **Administratorzy** z menu po lewej stronie.

    ![Administrator Administrator dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. W prawym górnym rogu, kliknij przycisk **Dodawanie administratora**:

    ![Administrator dostępu prywatnego rozwiązania Zscaler Dodaj administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. W **Dodawanie administratora** strony, wykonaj następujące czynności:

    ![Administrator dostępu prywatnego rozwiązania Zscaler użytkownika administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. W **Username** polu tekstowym wprowadź adres e-mail użytkownika, takich jak BrittaSimon@contoso.com.

    b. W **hasło** pole tekstowe, wpisz hasło.

    c. W **Potwierdź hasło** pole tekstowe, wpisz hasło.

    d. Wybierz **roli** jako **Administrator dostępu prywatnego rozwiązania Zscaler**.

    e. W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak BrittaSimon@contoso.com.

    f. W **Phone** pole tekstowe, wpisz numer telefonu.

    g. W **strefa czasowa** pola tekstowego, wybierz strefę czasową.

    h. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka rozwiązania Zscaler prywatny dostęp administratora na panelu dostępu, możesz powinna istnieć możliwość automatycznej rejestracji do rozwiązania Zscaler prywatny dostęp administratora dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

