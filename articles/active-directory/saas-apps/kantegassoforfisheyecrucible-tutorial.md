---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Kantega SSO dla FishEye/Crucible | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logakiem SSO firmy Kantega dla funkcji FishEye/Crucible.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f262f94c451d5dcffd933bdebb1374b8733b9fd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67595169"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Kantega SSO dla FishEye/Crucible

W tym samouczku dowiesz się, jak zintegrować usługę SSO kantega dla fisheye/crucible z usługą Azure Active Directory (Azure AD).
Integracja usługi SSO kantega dla FishEye/Crucible z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do usługi Kantega SSO dla FishEye/Crucible.
* Można włączyć automatyczne logowanie użytkowników do logowania jednokrotnego kantega dla fisheye/crucible (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Kantega SSO dla FishEye/Crucible, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Kantega SSO dla subskrypcji z obsługą logowania jednokrotnego FishEye/Crucible

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Kantega SSO for FishEye/Crucible obsługuje jednostki SSO zainicjowane przez **SP i IDP**

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Dodawanie sso Kantega dla FishEye/Crucible z galerii

Aby skonfigurować integrację usługi SSO Kantega dla FishEye/Crucible w usłudze Azure AD, musisz dodać kod SSO kantega dla FishEye/Crucible z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać funkcję SSO Kantega dla FishEye/Crucible z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Kantega SSO dla FishEye/Crucible**, wybierz **Opcję SSO Kantega dla FishEye/Crucible** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Kantega SSO dla FishEye/Crucible na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Kantega SSO dla FishEye / Crucible na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie Anso logowania kantega dla fisheye/crucible.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego kantega dla fisheye/crucible, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne kantega dla rejestracji jednokrotnej FishEye/Crucible](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz narzędzie SSO Kantega dla użytkownika testowego FishEye/Crucible](#create-kantega-sso-for-fisheyecrucible-test-user)** — aby mieć odpowiednik Britta Simon w prochu SSO Kantega dla FishEye/Crucible, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego Kantega dla fisheye/crucible, wykonaj następujące kroki:

1. W [portalu Azure](https://portal.azure.com/), na stronie integracji aplikacji **Kantega SSO dla aplikacji FishEye/Crucible** wybierz opcję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Kantega SSO dla FishEye / Crucible Domain i adresów URL informacje o logowaniach jednokrotnych](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![Kantega SSO dla FishEye / Crucible Domain i adresów URL informacje o logowaniach jednokrotnych](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Wartości te są odbierane podczas konfiguracji wtyczki FishEye/Crucible, która została wyjaśniona w dalszej części samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **SSO konfigurowania Kantega dla FishEye/Crucible** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Konfigurowanie logowania jednokrotnego kantega dla rejestracji pojedynczej FishEye/Crucible

1. W innym oknie przeglądarki internetowej zaloguj się na lokalnym serwerze FishEye/Crucible jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. W sekcji Ustawienia systemowe kliknij pozycję **Znajdź nowe dodatki**. 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Wyszukaj **program Kantega SSO dla Tygla** i kliknij przycisk **Zainstaluj,** aby zainstalować nową wtyczkę SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. Rozpocznie się instalacja wtyczki. 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę. 

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. W sekcji **SAML.** Wybierz **usługę Azure Active Directory (Azure AD)** z listy rozwijanej Dodaj **dostawcę tożsamości.**

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowy**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. W sekcji **Właściwości aplikacji** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Skopiuj wartość **identyfikatora URI aplikacji** i użyj jej jako **identyfikatora, adresu URL odpowiedzi i adresu URL logowania w** sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. Kliknij przycisk **alej**.

1. W sekcji **Importowanie metadanych** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Wybierz **plik metadanych na moim komputerze**i przekaż plik metadanych, który został pobrany z witryny Azure portal.

    b. Kliknij przycisk **alej**.

1. W sekcji **Lokalizacja nazwy i syłowienia należy** wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w pola tekstowym **nazwy dostawcy tożsamości** (np. usługi Azure AD).

    b. Kliknij przycisk **alej**.

1. Sprawdź certyfikat podpisywania i kliknij przycisk **Dalej**.   

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. W sekcji **Konta użytkowników FishEye** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Wybierz **pozycję Utwórz użytkowników w katalogu wewnętrznym FishEye w razie potrzeby** i wprowadź odpowiednią nazwę grupy dla użytkowników (może to być wiele nie. grup oddzielonych przecinkiem).

    b. Kliknij przycisk **alej**.

1. Kliknij przycisk **Zakończ**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. W sekcji **Znane domeny dla usługi Azure AD** wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Wybierz **pozycję Znane domeny** z lewego panelu strony.

    b. Wprowadź nazwę domeny w polach **tekstowych Znane domeny.**

    d. Kliknij przycisk **Zapisz**.

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do logowania jednokrotnego Kantega dla FishEye/Crucible.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **wszystkie aplikacje,** a następnie wybierz pozycję **Kantega SSO dla aplikacji FishEye/Crucible**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **opcję Kantega SSO dla FishEye/Crucible**.

    ![Link SSO Kantega dla FishEye/Crucible na liście Aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Tworzenie identyfikatora SSO Kantega dla użytkownika testowego FishEye/Crucible

Aby umożliwić użytkownikom usługi Azure AD logowanie się do FishEye/Crucible, muszą one być aprowizowane w FishEye/Crucible. W Kantega SSO dla FishEye / Crucible, inicjowanie obsługi administracyjnej jest ręcznym zadaniem.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się na lokalnym serwerze Tygla jako administrator.

1. Najedź kursorem na trybiku i kliknij **przycisk Użytkownicy**.

    ![Dodawanie pracownika](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. W sekcji **Karta Użytkownicy** kliknij pozycję **Dodaj użytkownika**.

    ![Dodawanie pracownika](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Na stronie okna dialogowego **Dodawanie nowego użytkownika** wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Nazwa wyświetlana** wpisz nazwę wyświetlaną użytkownika, taką jak Britta Simon.

    d. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. W polach tekstowych **Potwierdź hasło** ponownie wejdź ponownie pod hasło użytkownika.

    f. Kliknij przycisk **Dodaj**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SSO Kantega dla FishEye/Crucible w panelu dostępu, powinieneś zostać automatycznie zalogowany do SSO Kantega dla FishEye/Crucible, dla którego skonfigurujesz logowanie jednośmiętne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)