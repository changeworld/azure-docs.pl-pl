---
title: 'Samouczek: Integracja usługi Azure Active Directory z zdlar beta | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Zscaler Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943292"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Samouczek: Integracja usługi Azure Active Directory z zdlar beta

Z tego samouczka dowiesz się, jak zintegrować usługę Zscaler Beta z usługą Azure Active Directory (Azure AD).
Po zintegrowaniu wersji beta z programem Zscaler z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do wersji beta Zscaler.
* Zezwalaj użytkownikom na automatyczne logowanie się do wersji Beta zscaler za pomocą ich kont usługi Azure AD. Ta kontrola dostępu jest nazywana logowanie jednokrotne (Logowanie jednokrotne).
* Zarządzaj kontami w jednej centralnej lokalizacji za pomocą witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji z usługą Azure AD na temat oprogramowania jako usługi ,zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą Zscaler Beta są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja beta Zscaler, która używa logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zscaler Beta obsługuje syto inicjowane przez SP.
* Zscaler Beta obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Dodawanie wersji beta programu Zscaler z portalu Azure Marketplace

Aby skonfigurować integrację wersji beta programu Zscaler z usługą Azure AD, dodaj wersję Beta programu Zscaler z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać wersję beta Zscaler z portalu Azure Marketplace, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku nawigacji wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler Beta**. Wybierz **zklar beta** z panelu wyników, a następnie wybierz pozycję **Dodaj**.

     ![Usługa Zscaler Beta na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą Zscaler Beta na podstawie użytkownika testowego Britta Simon.
Aby logowanie jednokrotne działało, nawiązuj relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w wersji Beta programu Zscaler.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą wersji Beta programu Zscaler, wykonaj następujące elementy konstrukcyjne:

- [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
- [Skonfiguruj logowanie jednokrotne Zscaler Beta,](#configure-zscaler-beta-single-sign-on) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
- [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
- [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
- [Utwórz użytkownika beta programu Zscaler,](#create-a-zscaler-beta-test-user) aby mieć odpowiednik Britta Simon w wersji beta Zscaler, która jest połączona z reprezentacją użytkownika usługi Azure AD.
- [Testowanie logowania jednokrotnego](#test-single-sign-on) — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą wersji Beta programu Zscaler, wykonaj następujące kroki.

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usługi **Zscaler Beta** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie łącza logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML** wykonaj ten krok:

    ![Informacje o domenie beta i adresach URL Zscaler](common/sp-intiated.png)

    - W polu **Zaloguj się na adres URL** wprowadź adres URL używany przez użytkowników do logowania się do aplikacji Zscaler Beta.

    > [!NOTE]
    > Wartość nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistej wartości adresu URL Logowania. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy klienta Zscaler Beta.](https://www.zscaler.com/company/contact)

5. Aplikacja Zscaler Beta oczekuje potwierdzeń SAML w określonym formacie. Należy dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Wybierz **pozycję Edytuj,** aby otworzyć okno dialogowe **Atrybuty użytkownika.**

    ![Okno dialogowe Atrybuty użytkownika](common/edit-attribute.png)

6. Aplikacja Zscaler Beta oczekuje, że kilka atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli.
    
    | Nazwa | Atrybut źródłowy | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Okno dialogowe Oświadczenia użytkownika](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    d. Pozostaw pole **Obszaru nazw** puste.

    d. W obszarze **Źródło**wybierz pozycję **Atrybut**.

    e. Na liście **Atrybut źródłowy** podaj wartość atrybutu pokazanego dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Wybierz **pozycję Zapisz**.

    > [!NOTE]
    > Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [Konfigurowanie oświadczenia roli](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz,** aby pobrać **certyfikat (Base64).** Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **Konfigurowanie wersji beta programu Zscaler** skopiuj adresy URL potrzebne do spełnienia wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - Adres URL logowania
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurowanie logowania jednokrotnego zscaler beta

1. Aby zautomatyzować konfigurację w wersji Zscaler Beta, zainstaluj **rozszerzenie przeglądarki My Apps Secure Sign-in,** wybierając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki **wybranie opcji Skonfiguruj wersję beta zscaler** kieruje cię do aplikacji Zscaler Beta. Stamtąd podaj poświadczenia administratora, aby zalogować się do wersji beta Zscaler. Rozszerzenie przeglądarki automatycznie konfiguruje aplikację dla Ciebie i automatyzuje kroki od 3 do 6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Aby ręcznie skonfigurować wersję Zscaler Beta, otwórz nowe okno przeglądarki internetowej. Zaloguj się do witryny firmy Zscaler Beta jako administrator i wykonaj następujące kroki.

4. Przejdź do ustawień > **uwierzytelniania** > **Authentication Settings** **administracyjnego**i wykonaj następujące kroki.
   
    ![Administracja](./media/zscaler-beta-tutorial/ic800206.png "Administracja")

    a. W obszarze **Typ uwierzytelniania**wybierz pozycję **SAML**.

    b. Wybierz **pozycję Konfiguruj SAML**.

5. W oknie **Edytowanie SAML** wykonaj następujące czynności: 
            
    ![Zarządzanie uwierzytelnianiem użytkowników &](./media/zscaler-beta-tutorial/ic800208.png "Zarządzanie uwierzytelnianiem użytkowników &")
    
    a. W polu **adresu URL portalu SAML** wklej **adres URL logowania** skopiowany z witryny Azure portal.

    b. W polu **Atrybut Nazwa logowania** wprowadź identyfikator **nazw**.

    d. W polu **Publiczny certyfikat SSL** wybierz pozycję **Przekaż,** aby przekazać certyfikat podpisywania SAML platformy Azure pobrany z witryny Azure portal.

    d. Włącz **automatyczne inicjowanie obsługi administracyjnej SAML**.

    e. W polu **Atrybut nazwy wyświetlanej użytkownika** wprowadź nazwę **displayName,** jeśli chcesz włączyć automatyczne aprovisionowanie SAML dla atrybutów displayName.

    f. W polu **Atrybut nazwy grupy** wprowadź element **memberOf,** jeśli chcesz włączyć automatyczne aprovisioning SAML dla atrybutów memberOf.

    g. W polu **Atrybut nazwa działu** wprowadź **dział,** jeśli chcesz włączyć automatyczne aprovisioning SAML dla atrybutów działów.

    h. Wybierz **pozycję Zapisz**.

6. Na stronie okna dialogowego **Konfigurowanie uwierzytelniania użytkowników** wykonaj następujące czynności:

    ![Menu aktywacji i przycisk Aktywuj](./media/zscaler-beta-tutorial/ic800207.png)

    a. Umieść wskaźnik myszy na menu **Aktywacja** w lewym dolnym rogu.

    b. Wybierz **pozycję Aktywuj**.

## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer, wykonaj następujące kroki.

1. Uruchom program **Internet Explorer**.

2. Wybierz **opcje internetowe** z menu **Narzędzia,** aby otworzyć okno dialogowe **Opcje internetowe.** 
    
     ![Okno dialogowe Opcje internetowe](./media/zscaler-beta-tutorial/ic769492.png "Opcje internetowe")

3. Wybierz kartę **Połączenia.** 
  
     ![Karta Połączenia](./media/zscaler-beta-tutorial/ic769493.png "Połączenia")

4. Wybierz **ustawienia sieci LAN,** aby otworzyć okno dialogowe **Ustawienia sieci lokalnej (LAN).**

5. W sekcji **Serwer proxy** wykonaj następujące kroki: 
   
    ![Sekcja serwera proxy](./media/zscaler-beta-tutorial/ic769494.png "Serwer proxy")

    a. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN.**

    b. W polu **Adres** wprowadź **bramę. Zscaler Beta.net**.

    d. W polu **Port** wprowadź **80**.

    d. Zaznacz pole wyboru **Pomijanie serwera proxy dla adresów lokalnych.**

    e. Wybierz **przycisk OK,** aby zamknąć okno dialogowe **Ustawienia sieci lokalnej (LAN).**

6. Wybierz **przycisk OK,** aby zamknąć okno dialogowe **Opcje internetowe.**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Utwórz użytkownika testowego w witrynie Azure portal o nazwie Britta Simon.

1. W witrynie Azure portal w lewym okienku wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszyscy użytkownicy linki](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** podaj wartość `brittasimon@yourcompanydomain.extension`. Może to być na przykład BrittaSimon@contoso.com.

    d. Zaznacz pole wyboru **Pokaż hasło.** Zapisz wartość wyświetlaną w polu **Hasło.**

    d. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Włącz Britta Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do Zscaler Beta.

1. W portalu Azure wybierz **aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje** > **Zscaler Beta**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wprowadź i wybierz **Zscaler Beta**.

    ![Zscaler Beta link na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Łącze Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Przycisk Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz z listy użytkownika takiego jak **Britta Simon.** Następnie wybierz pozycję **Wybierz** u dołu ekranu.

    ![Okno dialogowe Użytkownicy i grupy](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. W oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę użytkownika na liście. Następnie wybierz pozycję **Wybierz** u dołu ekranu.

    ![Okno dialogowe Wybieranie roli](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

    ![Okno dialogowe Dodawanie przydziału](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Tworzenie użytkownika beta testów Zscaler

W tej sekcji użytkownik Britta Simon jest tworzony w Zscaler Beta. Usługa Zscaler Beta obsługuje **aprowizację użytkowników typu just in time**, która jest domyślnie włączona. W tej sekcji nie ma nic do zrobienia. Jeśli użytkownik jeszcze nie istnieje w usłudze Zscaler Beta, to zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Aby ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Przetestuj konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Zscaler Beta w Panelu dostępu należy automatycznie zalogować się do wersji beta Zscaler, dla której skonfigurowano logującą się do systemu. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

