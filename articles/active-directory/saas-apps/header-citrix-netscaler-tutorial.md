---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Citrix datascaleer (uwierzytelnianie oparte na nagłówku) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Citrix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c442ca731ecb10f977c19b86cb32caded36659
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472163"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-header-based-authentication"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Citrix datascaleer (uwierzytelnianie na podstawie nagłówka)

W tym samouczku dowiesz się, jak zintegrować program Citrixow z Azure Active Directory (Azure AD). Po zintegrowaniu usługi Citrix datascaleer z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Citrix.
* Zezwól użytkownikom na automatyczne logowanie do usługi Citrixow przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Citrix.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program citrixow obsługuje zainicjowanie rejestracji jednokrotnej w programie **SP**

* Program citrixow obsługuje Inicjowanie obsługi użytkowników **just in Time**

- [Konfigurowanie logowania jednokrotnego platformy Citrix dla uwierzytelniania opartego na nagłówku](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Konfigurowanie logowania jednokrotnego dla platformy Citrix dla uwierzytelniania opartego na protokole Kerberos](citrix-netscaler-tutorial.md)


## <a name="adding-citrix-netscaler-from-the-gallery"></a>Dodawanie programu Citrix datascaleer z galerii

Aby skonfigurować integrację programu Citrix Cordova z usługą Azure AD, musisz dodać program Citrix Cordova z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **Citrix** .
1. Wybierz pozycję **Citrix Datascaleer** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Citrix

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Citrix BI przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Citrix Work.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Citrix, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-citrix-netscaler-sso)** w usłudze Citrix, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego](#create-citrix-netscaler-test-user)** dla programu Citrix BI, aby dysponować odpowiednikiem B. Simon w usłudze Citrix BI, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Citrix** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<<Your FQDN>>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta Citrix](https://www.citrix.com/contact/technical-support.html) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    > [!NOTE]
    > Aby można było korzystać z logowania jednokrotnego, te adresy URL powinny być dostępne z witryn publicznych. Aby enble usługę Azure AD w celu opublikowania tokenu na skonfigurowanym adresie URL usług ACS, należy włączyć zaporę lub inne ustawienia zabezpieczeń w obszarze usługi.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **adres URL metadanych federacji aplikacji**, skopiuj adres URL i Zapisz go w Notatniku.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aplikacja Citrix DataScale oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj** i Zmień mapowanie atrybutu.

    ![image](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja platformy Citrix nie oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji oświadczenia użytkownika w oknie dialogowym atrybuty użytkownika wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    1. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    1. Pozostaw pole **Przestrzeń nazw** puste.

    1. Dla opcji Źródło wybierz wartość **Atrybut**.

    1. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    1. Kliknij przycisk **OK**.

    1. Kliknij pozycję **Zapisz**.


1. W sekcji **Konfigurowanie platformy Citrix** , skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Citrix BI.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Citrix**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-citrix-netscaler-sso"></a>Konfigurowanie logowania jednokrotnego dla platformy Citrix

- [Konfigurowanie logowania jednokrotnego platformy Citrix dla uwierzytelniania opartego na nagłówku](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Konfigurowanie logowania jednokrotnego dla platformy Citrix dla uwierzytelniania opartego na protokole Kerberos](citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Publikowanie serwera sieci Web 

1. Utwórz **serwer wirtualny**.

    a. Przejdź do obszaru **Zarządzanie ruchem > równoważenia obciążenia > usługi**.
    
    b. Kliknij pozycję **Dodaj**.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/web01.png)

    d. Określ szczegóły serwera sieci Web, na którym są uruchomione następujące aplikacje:
    * **Nazwa usługi**
    * **Adres IP serwera/istniejący serwer**
    * **Protokół**
    * **Port**

     ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Konfigurowanie Load Balancer

1. Aby skonfigurować Load Balancer, wykonaj następujące czynności:

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/load01.png)

    a. Przejdź do obszaru **Zarządzanie ruchem > równoważenia obciążenia > serwery wirtualne**.

    b. Kliknij pozycję **Dodaj**.

    d. Określ szczegóły poniżej:

    * **Nazwa**
    * **Protokół**
    * **Adres IP**
    * **Port**
    * Kliknij przycisk **OK** .

### <a name="bind-virtual-server"></a>Powiązywanie serwera wirtualnego

Powiąż Load Balancer z utworzonym wcześniej serwerem wirtualnym.

![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/bind01.png)

![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Powiąż certyfikat

Ponieważ będziemy publikować tę usługę jako powiązanie protokołu SSL z certyfikatem serwera, Przetestuj aplikację.

![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/bind03.png)

![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil protokołu SAML Citrix ADC

### <a name="create-authentication-policy"></a>Utwórz zasady uwierzytelniania

1. Przejdź do pozycji **zabezpieczenia > AAA — ruch aplikacji > zasady > uwierzytelnianie > zasady uwierzytelniania**.

2. Kliknij przycisk **Dodaj** , a następnie określ szczegóły.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/policy01.png)

    a. Nazwa **zasad uwierzytelniania**.

    b. Wyrażenie: **true**.

    d. Typ akcji **SAML**.

    d. Akcja = kliknij przycisk **Dodaj** (postępuj zgodnie z instrukcjami Kreatora tworzenia serwera SAML uwierzytelniania).
    
    e. Kliknij pozycję Utwórz w **zasadach uwierzytelniania**.

### <a name="create-authentication-saml-server"></a>Tworzenie serwera SAML uwierzytelniania

1. Wykonaj poniższe czynności:

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/server01.png)

    a. Określ **nazwę**.

    b. Zaimportuj metadane (Określ adres URL metadanych Federacji z interfejsu użytkownika usługi Azure SAML, który został skopiowany z powyższych danych).
    
    d. Określ **nazwę wystawcy**.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-authentication-virtual-server"></a>Utwórz serwer wirtualny uwierzytelniania

1.  Przejdź do pozycji **zabezpieczenia > AAA-ruch aplikacji > > serwery wirtualne uwierzytelniania**.

2.  Kliknij przycisk **Dodaj** i wykonaj następujące czynności:

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/server02.png)

    a.  Podaj **nazwa**.

    b.  Wybierz opcję **bez adresu**.

    d.  Protokół **SSL**.

    d.  Kliknij przycisk **OK**.

    e.  Kliknij pozycję **Kontynuuj**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurowanie serwera wirtualnego uwierzytelniania do korzystania z usługi Azure AD

Należy zmodyfikować 2 sekcje wirtualnego serwera uwierzytelniania.

1.  **Zaawansowane zasady uwierzytelniania**

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/virtual01.png)

    a. Wybierz utworzone wcześniej **zasady uwierzytelniania** .

    b. Kliknij pozycję **bind**.

      ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/virtual02.png)

2. **Oparte na formularzach serwery wirtualne**

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/virtual03.png)

    a.  Musisz podać **nazwę FQDN** , ponieważ jest ona wymuszana przez interfejs użytkownika.

    b.  Wybierz **Load Balancer serwera wirtualnego** , który chcesz chronić za pomocą uwierzytelniania usługi Azure AD.

    d.  Kliknij pozycję **bind**.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Upewnij się, że klikniesz pozycję **gotowe** na stronie Konfiguracja wirtualnego serwera uwierzytelniania.

3. Sprawdź zmiany. Przejdź do adresu URL aplikacji. Powinna zostać wyświetlona strona logowania dzierżawy zamiast wcześniej nieuwierzytelnionego dostępu.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-header-based-authentication"></a>Konfigurowanie logowania jednokrotnego platformy Citrix dla uwierzytelniania opartego na nagłówku

### <a name="citrix-adc-configuration"></a>Konfiguracja Citrix ADC

### <a name="create-an-rewrite-action"></a>Utwórz akcję ponownego zapisu

1. Przejdź do **AppExpert > ponownie napisz > akcje ponownego zapisywania**.
 
    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header01.png)

2.  Kliknij pozycję **Dodaj**.

    a.  Określ **nazwę**.

    b.  Typ = **INSERT_HTTP_HEADER**.

    d.  Określ **nazwę nagłówka** (SecretID w tym przykładzie).

    d.  Określ wyrażenie **AAA. Użytkownicy. ATRYBUT (`mySecretID`)** , gdzie **mySecretID** to Deklaracja SAML usługi Azure AD wysyłana do programu Citrix ADC.

    e.  Kliknij przycisk **Utwórz**.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header02.png)
 
### <a name="create-a-rewrite-policy"></a>Utwórz zasady ponownego zapisywania.

1.  Przejdź do **AppExpert > ponownie Zapisz zasady > zapisywania**.
 
    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header03.png)

2.  Kliknij pozycję **Dodaj**.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header04.png)

    a.  Określ **nazwę**.

    b.  Wybierz utworzoną wcześniej **akcję** .

    s. Wyrażenie określa **wartość true**.

    d.  Kliknij przycisk **Utwórz**.

### <a name="bind-rewrite-policy-to-virtual-servers"></a>Powiąż zasady ponownego zapisywania z serwerami wirtualnymi

1. Aby powiązać zasady ponownego zapisu z określonym serwerem wirtualnym przy użyciu graficznego interfejsu użytkownika.

2. Przejdź do **zarządzania ruchem > równoważenia obciążenia > serwerów wirtualnych**.

3. W okienku szczegółów listy serwerów wirtualnych wybierz **serwer wirtualny** , do którego chcesz powiązać zasady ponownego zapisu, a następnie kliknij przycisk **Otwórz**.

4. W oknie dialogowym Konfigurowanie serwera wirtualnego (równoważenia obciążenia) wybierz kartę **zasady** . Wszystkie zasady skonfigurowane w ramach skalowania w sieci są widoczne na liście.
 
    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header06.png)

5.  Zaznacz **pole wyboru** obok nazwy zasad, które chcesz powiązać z tym serwerem wirtualnym.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header07.png)

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header08.png)
 
6.  Kliknij przycisk **OK**. Na pasku stanu zostanie wyświetlony komunikat informujący o pomyślnym skonfigurowaniu zasad.

### <a name="modify-saml-server-to-extract-attributes-from-claim"></a>Zmodyfikuj serwer SAML, aby wyodrębnić atrybuty z żądania

1.  Przejdź do pozycji **zabezpieczenia > AAA-ruch aplikacji > zasady > uwierzytelnianie > zaawansowane zasady > działania > serwerów**.

2.  Wybierz odpowiedni **serwer uwierzytelniania SAML** dla aplikacji.
 
    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header09.png)

3. W sekcji atrybutu wpisz atrybuty SAML, które mają zostać wyodrębnione przy użyciu ",". W przypadku określenia atrybutu o nazwie **mySecretID**.
 
    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header10.png)

4. Sprawdź, czy wszyscy uzyskują dostęp do aplikacji.

    ![Konfiguracja platformy Citrix](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-citrix-netscaler-test-user"></a>Utwórz użytkownika testowego platformy Citrix

W tej sekcji użytkownik o nazwie B. Simon został utworzony w programie Citrix. Program citrixow obsługuje Inicjowanie obsługi użytkowników just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Citrix, zostanie utworzony nowy po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem obsługi klienta Citrix](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Citrix datascalenia w panelu dostępu należy automatycznie zalogować się do usługi Citrix, w której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Citrixow w usłudze Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie logowania jednokrotnego dla platformy Citrix dla uwierzytelniania opartego na protokole Kerberos](citrix-netscaler-tutorial.md)
