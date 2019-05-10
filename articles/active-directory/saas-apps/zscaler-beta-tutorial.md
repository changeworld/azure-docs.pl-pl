---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Zscaler Beta | Microsoft Docs'
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
ms.openlocfilehash: f31361dc3d7e24092677f1a78b2c405ae84578ed
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230057"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Samouczek: integracja usługi Azure Active Directory z usługą Zscaler Beta

Z tego samouczka dowiesz się, jak zintegrować usługę Zscaler Beta z usługą Azure Active Directory (Azure AD).
Po zintegrowaniu rozwiązania Zscaler w wersji Beta z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler w wersji Beta.
* Umożliwia użytkownikom automatyczne logowanie do rozwiązania Zscaler Beta za pomocą kont usługi Azure AD. Ta kontrola dostępu do nosi nazwę logowania jednokrotnego (SSO).
* Zarządzanie Twoimi kontami, w jednej centralnej lokalizacji, za pomocą witryny Azure portal.

Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą Zscaler Beta są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja rozwiązania Zscaler w wersji Beta, która korzysta z logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązania Zscaler Beta obsługuje logowanie Jednokrotne zainicjowane przez SP.
* Rozwiązania Zscaler Beta obsługę użytkownika just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Dodaj rozwiązania Zscaler w wersji Beta w portalu Azure Marketplace

Aby skonfigurować integrację rozwiązania Zscaler w wersji Beta w usłudze Azure AD, należy dodać rozwiązania Zscaler w wersji Beta w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS.

Aby dodać rozwiązania Zscaler w wersji Beta w portalu Azure Marketplace, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Beta rozwiązania Zscaler**. Wybierz **Beta rozwiązania Zscaler** z panelu wyników, a następnie wybierz **Dodaj**.

     ![Usługa Zscaler Beta na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler Beta oparte na użytkownika testowego Britta Simon.
Dla logowania jednokrotnego do pracy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w wersji Beta rozwiązania Zscaler.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler w wersji Beta, wykonaj poniższe bloki konstrukcyjne:

- [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
- [Konfigurowanie rozwiązania Zscaler Beta logowania jednokrotnego](#configure-zscaler-beta-single-sign-on) do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
- [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
- [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
- [Tworzenie użytkownika testowego Beta rozwiązania Zscaler](#create-a-zscaler-beta-test-user) mieć odpowiednikiem Britta Simon w wersji Beta rozwiązania Zscaler, połączonego z usługi Azure AD reprezentacja użytkownika.
- [Testowanie logowania jednokrotnego](#test-single-sign-on) — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler w wersji Beta, wykonaj następujące kroki.

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usługi **Zscaler Beta** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** sekcji, wykonaj poniższe czynności:

    ![Rozwiązania Zscaler w wersji Beta, domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/sp-intiated.png)

    - W **adres URL logowania** wprowadź adres URL używany przez użytkowników do logowania do aplikacji rozwiązania Zscaler w wersji Beta.

    > [!NOTE]
    > Wartość nie jest prawdziwe. Zaktualizuj wartość za pomocą rzeczywistych logowanie jednokrotne wartość adresu URL. Aby uzyskać wartość, skontaktuj się z pomocą [zespołem pomocy technicznej klienta Beta rozwiązania Zscaler](https://www.zscaler.com/company/contact).

5. Aplikacja rozwiązania Zscaler Beta oczekuje twierdzenia SAML w określonym formacie. Mapowania atrybutów niestandardowych należy dodać do konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Wybierz **Edytuj** otworzyć **atrybutów użytkownika** okno dialogowe.

    ![Atrybuty użytkownika, okno dialogowe](common/edit-attribute.png)

6. Aplikacja rozwiązania Zscaler Beta oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W **oświadczenia użytkownika** sekcji **atrybutów użytkownika** okno dialogowe, wykonaj poniższe kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli.
    
    | Name (Nazwa) | Atrybut źródłowy | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Okno dialogowe oświadczenia użytkownika](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    c. Pozostaw **Namespace** puste pole.

    d. Aby uzyskać **źródła**, wybierz opcję **atrybutu**.

    e. Na liście **Atrybut źródłowy** podaj wartość atrybutu pokazanego dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [Konfigurowanie oświadczenia roli](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **certyfikat (Base64)** . Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W **konfigurowanie rozwiązania Zscaler Beta** sekcji, skopiuj adresy URL muszą zgodnie z wymaganiami dotyczącymi:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - Adres URL logowania
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurowanie rozwiązania Zscaler Beta logowania jednokrotnego

1. Aby zautomatyzować konfigurację w ramach rozwiązania Zscaler w wersji Beta, należy zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , wybierając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, wybierając **konfigurowanie rozwiązania Zscaler Beta** kieruje użytkownika do aplikacji rozwiązania Zscaler w wersji Beta. W tym miejscu podaj poświadczenia administratora do logowania do rozwiązania Zscaler w wersji Beta. Rozszerzenie przeglądarki automatycznie konfiguruje aplikację dla Ciebie i automatyzuje kroki od 3 do 6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Aby ręcznie skonfigurować rozwiązania Zscaler w wersji Beta, Otwórz nowe okno przeglądarki sieci web. Zaloguj się do witryny rozwiązania Zscaler Beta firmy jako administrator, a następnie wykonaj następujące kroki.

4. Przejdź do **administracji** > **uwierzytelniania** > **ustawienia uwierzytelniania**i wykonaj następujące czynności.
   
    ![Administracja](./media/zscaler-beta-tutorial/ic800206.png "Administracja")

    a. W obszarze **typ uwierzytelniania**, wybierz opcję **SAML**.

    b. Wybierz **skonfigurować SAML**.

5. W **Edytuj SAML** okna, wykonaj następujące kroki: 
            
    ![Zarządzanie użytkownikami i uwierzytelnianiem](./media/zscaler-beta-tutorial/ic800208.png "Zarządzanie użytkownikami i uwierzytelnianiem")
    
    a. W **adres URL portalu SAML** pole, Wklej w **adres URL logowania** skopiowaną z witryny Azure portal.

    b. W **atrybutu nazwy logowania** wprowadź **NameID**.

    c. W **publicznego certyfikatu SSL** wybierz opcję **przekazywanie** można przekazać certyfikatu podpisywania SAML platformy Azure, pobrany z witryny Azure portal.

    d. Przełącz **Włącz SAML automatycznej aprowizacji**.

    e. W **atrybutu Nazwa wyświetlana użytkownika** wprowadź **displayName** Jeśli chcesz włączyć autoprovisioning SAML dla atrybutów displayName.

    f. W **atrybutu nazwy grupy** wprowadź **memberOf** Jeśli chcesz włączyć autoprovisioning SAML memberOf atrybutów.

    g. W **atrybutu nazwy działu** wprowadź **działu** Jeśli chcesz włączyć autoprovisioning SAML dla działu atrybutów.

    h. Wybierz pozycję **Zapisz**.

6. Na **Konfigurowanie uwierzytelniania użytkownika** okna dialogowego strony, wykonaj następujące kroki:

    ![Menu aktywacji i przycisk Aktywuj](./media/zscaler-beta-tutorial/ic800207.png)

    a. Umieść kursor nad **aktywacji** menu w lewym dolnym rogu.

    b. Wybierz **aktywować**.

## <a name="configure-proxy-settings"></a>Skonfiguruj ustawienia serwera proxy
Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer, wykonaj następujące kroki.

1. Uruchom program **Internet Explorer**.

2. Wybierz **Opcje internetowe** z **narzędzia** menu, aby otworzyć **Opcje internetowe** okno dialogowe. 
    
     ![Okno dialogowe Opcje internetowe](./media/zscaler-beta-tutorial/ic769492.png "Opcje internetowe")

3. Wybierz **połączeń** kartę. 
  
     ![Karta połączenia](./media/zscaler-beta-tutorial/ic769493.png "połączeń")

4. Wybierz **ustawienia sieci LAN** otworzyć **ustawienia sieci lokalnej (LAN)** okno dialogowe.

5. W **serwera Proxy** sekcji, wykonaj następujące kroki: 
   
    ![Sekcja serwera proxy](./media/zscaler-beta-tutorial/ic769494.png "serwera Proxy")

    a. Wybierz **Użyj serwera proxy dla sieci LAN** pole wyboru.

    b. W **adres** wprowadź **bramy. Rozwiązania Zscaler Beta.net**.

    c. W **portu** wprowadź **80**.

    d. Wybierz **używaj serwera proxy dla adresów lokalnych** pole wyboru.

    e. Wybierz **OK** zamknąć **ustawienia sieci lokalnej (LAN)** okno dialogowe.

6. Wybierz **OK** zamknąć **Opcje internetowe** okno dialogowe.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszystkie linki użytkowników](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące kroki:

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wprowadź `brittasimon@yourcompanydomain.extension`. Może to być na przykład BrittaSimon@contoso.com.

    c. Wybierz **hasło Show** pole wyboru. Zanotuj wartość, która wyświetla w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Włącz Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do rozwiązania Zscaler w wersji Beta.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > **Beta rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji, wprowadź i wybierz **Beta rozwiązania Zscaler**.

    ![Łącze rozwiązania Zscaler Beta na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Łączy użytkowników i grup](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika**. W **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Przycisk Dodaj użytkownika](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz użytkownika, takie jak **Britta Simon** z listy. Następnie wybierz **wybierz** w dolnej części ekranu.

    ![Okno dialogowe Użytkownicy i grupy](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. W **wybierz rolę** okna dialogowego Wybierz odpowiedniej roli użytkownika na liście. Następnie wybierz **wybierz** w dolnej części ekranu.

    ![Okno dialogowe Wybierz rolę](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

    ![Dodaj przypisanie, okno dialogowe](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Tworzenie użytkownika testowego Beta rozwiązania Zscaler

W tej sekcji użytkownika Britta Simon jest tworzony w wersji Beta rozwiązania Zscaler. Usługa Zscaler Beta obsługuje **aprowizację użytkowników typu just in time**, która jest domyślnie włączona. Nie ma nic robić w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w usłudze Zscaler Beta, to zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Aby ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Testowanie konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka rozwiązania Zscaler w wersji Beta w panelu dostępu użytkownik powinien automatyczne logowanie do rozwiązania Zscaler wersji Beta, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

