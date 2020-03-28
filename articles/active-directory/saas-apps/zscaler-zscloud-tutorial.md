---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Zscaler ZSCloud | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 43d7e58f0c267afe8a22c217d9800abb041df8cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68723055"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Zscaler ZSCloud

W tym samouczku dowiesz się, jak zintegrować Zscaler ZSCloud z usługą Azure Active Directory (Azure AD).
Integracja Zscaler ZSCloud z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, który ma dostęp do Zscaler ZSCloud.
* Możesz włączyć automatyczne logowanie użytkowników do Zscaler ZSCloud (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Zscaler ZSCloud, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego Zscaler ZSCloud

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zscaler ZSCloud obsługuje sso inicjowane przez **SP**

* Zscaler ZSCloud obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Dodawanie Zscaler ZSCloud z galerii

Aby skonfigurować integrację Zscaler ZSCloud z usługą Azure AD, należy dodać Zscaler ZSCloud z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Zscaler ZSCloud z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler ZSCloud**, wybierz **Zscaler ZSCloud** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Zscaler ZSCloud na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Zscaler ZSCloud na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Zscaler ZSCloud.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą zscalera ZSCloud, musisz wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj zscaler ZSCloud Logowanie jednokrotne](#configure-zscaler-zscloud-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Zscaler ZSCloud](#create-zscaler-zscloud-test-user)** — aby mieć odpowiednik Britta Simon w Zscaler ZSCloud, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą zscalera ZSCloud, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Zscaler ZSCloud** wybierz pozycję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o logach jednokrotnych domeny I adresów URL Zscaler ZSCloud](common/sp-signonurl.png)

    W pola tekstowym **Adresu URL logowania** wpisz adres URL używany przez użytkowników do logowania się do aplikacji ZScaler ZSCloud.

    > [!NOTE]
    > Musisz zaktualizować wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zscaler ZSCloud,](https://help.zscaler.com/) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Zscaler ZSCloud oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj,** aby otworzyć okno dialogowe **Atrybuty użytkownika.**

    ![image](common/edit-attribute.png)

6. Oprócz powyższej, aplikacja Zscaler ZSCloud oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Nazwa | Atrybut źródłowy |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.
    
    f. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management), aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **Konfigurowanie Zscaler ZSCloud** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-zscaler-zscloud-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Zscaler ZSCloud

1. Aby zautomatyzować konfigurację w Zscaler ZSCloud, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając przycisk Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator Zscaler ZSCloud** skieruje Cię do aplikacji Zscaler ZSCloud. Stamtąd podaj poświadczenia administratora, aby zalogować się do Zscaler ZSCloud. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja sso](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Zscaler ZSCloud, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Zscaler ZSCloud jako administrator i wykonaj następujące czynności:

4. Przejdź do obszaru **Administracja > Uwierzytelnianie > Ustawienia uwierzytelniania** i wykonaj następujące kroki:
   
    ![Administracja](./media/zscaler-zscloud-tutorial/ic800206.png "Administracja")

    a. W obszarze Typ uwierzytelniania wybierz pozycję **SAML**.

    b. Kliknij pozycję **Skonfiguruj język SAML**.

5. W oknie **Edytowanie języka SAML** wykonaj następujące kroki i kliknij pozycję Zapisz.  
            
    ![Zarządzanie uwierzytelnianiem użytkowników &](./media/zscaler-zscloud-tutorial/ic800208.png "Zarządzanie uwierzytelnianiem użytkowników &")
    
    a. W polu tekstowym **Adres URL portalu języka SAML** wklej **adres URL logowania** skopiowany z witryny Azure Portal.

    b. W polu tekstowym **Atrybut nazwy logowania** wprowadź identyfikator **NameID**.

    d. Kliknij pozycję **Przekaż**, aby przekazać certyfikat podpisywania języka SAML na platformie Azure, który został pobrany z witryny Azure Portal w obrębie **publicznego certyfikatu SSL**.

    d. Przełącz element **Włącz automatyczne aprowizowanie języka SAML**.

    e. W polu tekstowym **Atrybut nazwy wyświetlanej użytkownika** wprowadź ciąg **displayName**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu displayName.

    f. W polu tekstowym **Atrybut nazwy grupy** wprowadź ciąg **memberOf**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu memberOf.

    g. W polu **Atrybut nazwy działu** wprowadź ciąg **department**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu department.

    h. Kliknij przycisk **Zapisz**.

6. Na stronie okna dialogowanie **Konfigurowanie uwierzytelniania użytkownika** wykonaj następujące kroki:

    ![Administracja](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Kliknij pozycję **Aktywuj**.

## <a name="configuring-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurowanie ustawień serwera proxy w programie Internet Explorer

1. Uruchom program **Internet Explorer**.

2. Wybierz pozycję **Opcje internetowe** z menu **Narzędzia**, aby otworzyć okno dialogowe **Opcje internetowe**.   
    
     ![Opcje internetowe](./media/zscaler-zscloud-tutorial/ic769492.png "Opcje internetowe")

3. Kliknij kartę **Połączenia**.   
  
     ![Połączenia](./media/zscaler-zscloud-tutorial/ic769493.png "Połączenia")

4. Kliknij przycisk **Ustawienia sieci LAN**, aby otworzyć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

5. W sekcji Serwer proxy wykonaj następujące kroki:   
   
    ![Serwer proxy](./media/zscaler-zscloud-tutorial/ic769494.png "Serwer proxy")

    a. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN**.

    b. W polach tekstowych Adres wpisz **bramę. Zscaler ZSCloud.net**.

    d. W polu tekstowym Port wpisz **80**.

    d. Zaznacz pole wyboru **Nie używaj serwera proxy dla adresów lokalnych**.

    e. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

6. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Opcje internetowe**.

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

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do Zscaler ZSCloud.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Zscaler ZSCloud**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Zscaler ZSCloud**.

    ![Łącze Zscaler ZSCloud na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. W oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę użytkownika na liście, a następnie kliknij przycisk **Wybierz** znajdujący się u dołu ekranu.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Domyślna rola dostępu nie jest obsługiwana, ponieważ spowoduje to przerwanie inicjowania obsługi administracyjnej, więc nie można wybrać roli domyślnej podczas przypisywania użytkownika.

### <a name="create-zscaler-zscloud-test-user"></a>Tworzenie użytkownika testowego Zscaler ZSCloud

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Zscaler ZSCloud. Zscaler ZSCloud obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Zscaler ZSCloud, nowy jest tworzony po uwierzytelnieniu.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Zscaler ZSCloud](https://help.zscaler.com/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Zscaler ZSCloud w Panelu dostępu należy automatycznie zalogować się do Zscaler ZSCloud, dla którego skonfigurowano logującą się logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

