---
title: 'Samouczek: Integracja z logiem jednokrotnym usługi Azure Active Directory z usługą Citrix NetScaler (uwierzytelnianie oparte na protokół Kerberos) | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem Citrix NetScaler przy użyciu uwierzytelniania opartego na protokół Kerberos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785242a2cf51571a6d13b2b4691d33e46369bf94
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977910"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Samouczek: Integracja z logiem jednokrotnym usługi Azure Active Directory z usługą Citrix NetScaler (uwierzytelnianie oparte na protokół Kerberos)

W tym samouczku dowiesz się, jak zintegrować program Citrix NetScaler z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Citrix NetScaler z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do citrix NetScaler.
* Włącz automatyczne logowanie użytkowników do usługi Citrix NetScaler za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja citrix NetScaler z obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Samouczek zawiera następujące scenariusze:

* **Inicjowane przez sp** SSO dla Citrix NetScaler

* **Inicjowanie** obsługi administracyjnej dla citrix NetScaler w samą porę

* [Uwierzytelnianie oparte na protokół Kerberos dla programu Citrix NetScaler](#publish-the-web-server)

* [Uwierzytelnianie oparte na nagłówku dla programu Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Dodaj Citrix NetScaler z galerii

Aby zintegrować program Citrix NetScaler z usługą Azure AD, należy najpierw dodać citrix NetScaler do listy zarządzanych aplikacji SaaS z galerii:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.

1. W sekcji **Dodaj z galerii** wprowadź w polu wyszukiwania **pozycję Citrix NetScaler.**

1. W wynikach wybierz pozycję **Citrix NetScaler**, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu Citrix NetScaler

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą citrix NetScaler przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Citrix NetScaler.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą narzędzia Citrix NetScaler, wykonaj następujące elementy konstrukcyjne:

1. [Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso) — aby umożliwić użytkownikom korzystanie z tej funkcji.

    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować samouszeństwo usługi Azure AD z usługą B.Simon.

    1. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić B.Simon używać usługi Azure AD SSO.

1. [Skonfiguruj citrix NetScaler SSO](#configure-citrix-netscaler-sso) - aby skonfigurować ustawienia logowania sytego po stronie aplikacji.

    * [Utwórz użytkownika testowego Citrix NetScaler](#create-a-citrix-netscaler-test-user) — aby mieć odpowiednik B.Simon w Citrix NetScaler, który jest połączony z reprezentacją użytkownika usługi Azure AD.

1. [Test SSO](#test-sso) - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć usługę Azure AD SSO przy użyciu witryny Azure portal, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)w okienku integracji aplikacji **Citrix NetScaler** w obszarze **Zarządzanie**wybierz pozycję **Logowanie jednokrotne**.

1. W okienku **Wybieranie metody logowania jednokrotnego** wybierz pozycję **SAML**.

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **edycji** pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** aby skonfigurować aplikację w trybie **inicjowanym przez IDP:**

    1. W polu tekstowym **Identyfikator** wprowadź adres URL zawierający następujący wzorzec:`https://<Your FQDN>`

    1. W polu tekstowym **Odpowiedz na adres URL** wprowadź adres URL zawierający następujący wzorzec:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Aby skonfigurować aplikację w trybie **inicjowanym przez dodatek,** wybierz pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujący krok:

    * W polu **tekstowym Zaloguj się adres URL** wprowadź adres URL zawierający następujący wzorzec:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Adresy URL używane w tej sekcji nie są wartościami rzeczywistymi. Zaktualizuj te wartości za pomocą rzeczywistych wartości identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Citrix NetScaler,](https://www.citrix.com/contact/technical-support.html) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.
    > * Aby skonfigurować sytą witrynę SSO, adresy URL muszą być dostępne z publicznych witryn sieci Web. Należy włączyć zapory lub inne ustawienia zabezpieczeń po stronie Citrix NetScaler, aby enble azure AD, aby opublikować token w skonfigurowanym adresie URL.

1. W okienku **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Certyfikat podpisywania SAML** dla **adresu URL metadanych federacji aplikacji**skopiuj adres URL i zapisz go w Notatniku.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie programu Citrix NetScaler** skopiuj odpowiednie adresy URL na podstawie wymagań użytkownika.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. W menu po lewej stronie w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

1. Wybierz **pozycję Nowy użytkownik** w górnej części okienka.

1. W **ucho użytkownikowi** wykonaj następujące czynności:

   1. W **yjmij nazwę**, wprowadź `B.Simon`.  

   1. W przypadku nazwy _username@companydomain.extension_ **użytkownika**wprowadź . Na przykład `B.Simon@contoso.com`.

   1. Zaznacz pole wyboru **Pokaż hasło,** a następnie zapisz lub skopiuj wartość wyświetlaną w **polu Hasło**.

   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika B.Simon do korzystania z usługi Azure SSO, przyznając użytkownikowi dostęp do Citrix NetScaler.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.

1. Na liście aplikacji wybierz pozycję **Citrix NetScaler**.

1. W przeglądzie aplikacji w obszarze **Zarządzanie**wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy **Użytkownicy.** Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie wybierz pozycję **Wybierz**.

1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-citrix-netscaler-sso"></a>Konfigurowanie sytcha pamięci SSO programu Citrix NetScaler

Wybierz łącze do kroków dla rodzaju uwierzytelniania, które chcesz skonfigurować:

- [Konfigurowanie uwierzytelniania samousznikowego programu Citrix NetScaler dla uwierzytelniania opartego na protokół Kerberos](#publish-the-web-server)

- [Konfigurowanie syd w trybie samosuw programu Citrix NetScaler do uwierzytelniania opartego na nagłówku](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publikowanie serwera sieci Web 

Aby utworzyć serwer wirtualny:

1. Wybierz pozycję**Usługi****równoważenia** > obciążenia **zarządzania** > ruchem .
    
1. Wybierz pozycję **Dodaj**.

    ![Konfiguracja citrix NetScaler — okienko Usługi](./media/citrix-netscaler-tutorial/web01.png)

1. Ustaw następujące wartości dla serwera sieci web, na który są uruchomione aplikacje:

   * **Nazwa usługi**
   * **Adres IP serwera/ istniejący serwer**
   * **Protokół**
   * **Portu**

### <a name="configure-the-load-balancer"></a>Konfigurowanie modułu równoważenia obciążenia

Aby skonfigurować moduł równoważenia obciążenia:

1. Przejdź do pozycji**Równoważenie** > obciążenia **zarządzania** > ruchem **.**

1. Wybierz pozycję **Dodaj**.

1. Ustaw następujące wartości zgodnie z opisem na poniższym zrzucie ekranu:

    * **Nazwa**
    * **Protokół**
    * **Adres IP**
    * **Portu**

1. Kliknij przycisk **OK**.

    ![Konfiguracja programu Citrix NetScaler — okienko Ustawienia podstawowe](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Powiąż serwer wirtualny

Aby powiązać moduł równoważenia obciążenia z serwerem wirtualnym:

1. W okienku **Usługi i grupy usług** wybierz opcję **Powiązanie usługi serwera wirtualnego Bez równoważenia obciążenia**.

   ![Konfiguracja programu Citrix NetScaler — okienko powiązania usługi równoważenia serwera wirtualnego](./media/citrix-netscaler-tutorial/bind01.png)

1. Sprawdź ustawienia, jak pokazano na poniższym zrzucie ekranu, a następnie wybierz pozycję **Zamknij**.

   ![Konfiguracja Citrix NetScaler — weryfikowanie powiązania usług serwera wirtualnego](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Powiąż certyfikat

Aby opublikować tę usługę jako SSL, powiąż certyfikat serwera, a następnie przetestuj aplikację:

1. W obszarze **Certyfikat**wybierz pozycję **Brak certyfikatu serwera**.

   ![Konfiguracja programu Citrix NetScaler — okienko certyfikatu serwera](./media/citrix-netscaler-tutorial/bind03.png)

1. Sprawdź ustawienia, jak pokazano na poniższym zrzucie ekranu, a następnie wybierz pozycję **Zamknij**.

   ![Konfiguracja Citrix NetScaler — weryfikowanie certyfikatu](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil Citrix ADC SAML

Aby skonfigurować profil CItrix ADC SAML, wykonaj następujące sekcje.

### <a name="create-an-authentication-policy"></a>Tworzenie zasad uwierzytelniania

Aby utworzyć zasady uwierzytelniania:

1. Przejdź do **security** > **AAA — Zasady** > **uwierzytelniania**zasad**uwierzytelniania** > **zasad** > ruchu aplikacji .

1. Wybierz pozycję **Dodaj**.

1. W okienku **Tworzenie zasad uwierzytelniania** wprowadź lub wybierz następujące wartości:

    * **Nazwa**: Wprowadź nazwę zasad uwierzytelniania.
    * **Akcja**: Wprowadź **SAML**, a następnie wybierz pozycję **Dodaj**.
    * **Wyrażenie**: Wprowadź **wartość true**.     
    
    ![Konfiguracja programu Citrix NetScaler — tworzenie okienka zasad uwierzytelniania](./media/citrix-netscaler-tutorial/policy01.png)

1. Wybierz **pozycję Utwórz**.

### <a name="create-an-authentication-saml-server"></a>Tworzenie serwera SAML uwierzytelniania

Aby utworzyć serwer SAML uwierzytelniania, przejdź do **okienka Utwórz serwer SAML uwierzytelniania,** a następnie wykonaj następujące kroki:

1. W **yjmij nazwę**serwera SAML uwierzytelniania.

1. W obszarze **Eksportowanie metadanych SAML:**

   1. Zaznacz pole wyboru **Importuj metadane.**

   1. Wprowadź adres URL metadanych federacji z interfejsu użytkownika saml platformy Azure, który został skopiowany wcześniej.
    
1. W przypadku **nazwy wystawcy**wprowadź odpowiedni adres URL.

1. Wybierz **pozycję Utwórz**.

![Konfiguracja programu Citrix NetScaler — tworzenie okienka serwera SAML uwierzytelniania](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Tworzenie serwera wirtualnego uwierzytelniania

Aby utworzyć serwer wirtualny uwierzytelniania:

1.  Przejdź do **programu Security** > **AAA — Serwery** > **wirtualne uwierzytelniania** > **uwierzytelniania****zasad** > ruchu aplikacji .

1.  Wybierz **pozycję Dodaj**, a następnie wykonaj następujące czynności:

    1. W **yjmij nazwę**serwera wirtualnego uwierzytelniania.

    1. Zaznacz pole wyboru **Nie adresowalne.**

    1. W przypadku **protokołu**wybierz opcję **SSL**.

    1. Kliknij przycisk **OK**.
    
1. Wybierz przycisk **Kontynuuj**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurowanie serwera wirtualnego uwierzytelniania do korzystania z usługi Azure AD

Zmodyfikuj dwie sekcje serwera wirtualnego uwierzytelniania:

1.  W okienku **Zasady uwierzytelniania zaawansowanego** wybierz pozycję **Brak zasad uwierzytelniania**.

    ![Konfiguracja programu Citrix NetScaler — okienko Zasady uwierzytelniania zaawansowanego](./media/citrix-netscaler-tutorial/virtual01.png)

1. W okienku **Powiązanie zasad** wybierz zasadę uwierzytelniania, a następnie wybierz pozycję **Bind**.

    ![Konfiguracja programu Citrix NetScaler — okienko powiązanie zasad](./media/citrix-netscaler-tutorial/virtual02.png)

1. W okienku **Serwery wirtualne oparte** na formularzach wybierz pozycję **Brak równoważenia obciążenia serwera wirtualnego**.

    ![Konfiguracja programu Citrix NetScaler — okienko Serwery wirtualne oparte na formularzach](./media/citrix-netscaler-tutorial/virtual03.png)

1. W przypadku **nazwy FQDN uwierzytelniania**wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) (wymagane).

1. Wybierz serwer wirtualny równoważenia obciążenia, który chcesz chronić za pomocą uwierzytelniania usługi Azure AD.

1. Wybierz **opcję Powiąż**.

    ![Konfiguracja programu Citrix NetScaler — okienko równoważenia obciążenia serwera wirtualnego](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Pamiętaj, aby wybrać opcję **Gotowe** w okienku **Konfiguracja serwera wirtualnego uwierzytelniania.**

1. Aby zweryfikować zmiany, w przeglądarce przejdź do adresu URL aplikacji. Powinna zostać wyświetlona strona logowania dzierżawy zamiast nieuwierzytynego dostępu, który widziałbyś wcześniej.

    ![Konfiguracja Citrix NetScaler — strona logowania w przeglądarce internetowej](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Konfigurowanie uwierzytelniania samousznikowego programu Citrix NetScaler dla uwierzytelniania opartego na protokół Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Tworzenie konta delegowania Protokołu Kerberos dla usługi Citrix ADC

1. Utwórz konto użytkownika (w tym przykładzie używamy _AppDelegation)._

    ![Konfiguracja programu Citrix NetScaler — okienko Właściwości](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Skonfiguruj nazwę SPN hosta dla tego konta. 

    Przykład: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    W tym przykładzie:

    * `IDENTT.WORK`jest domeną FQDN.
    * `identt`jest nazwą netbios domeny.
    * `appdelegation`to nazwa konta użytkownika delegowania.

1. Skonfiguruj delegowanie serwera sieci web, jak pokazano na poniższym zrzucie ekranu:
 
    ![Konfiguracja programu Citrix NetScaler — delegowanie w obszarze Właściwości okienka](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > W przykładzie zrzutu ekranu nazwa wewnętrznego serwera sieci Web z witryną Zintegrowanego uwierzytelniania systemu Windows (WIA) to _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (konta delegacji Kerberos)

Aby skonfigurować konto Citrix NetScaler AAA KCD:

1.  Przejdź do **konta Citrix Gateway** > **AAA KCD (Kerberos Constrained Delegation).**

1.  Wybierz **pozycję Dodaj**, a następnie wprowadź lub wybierz następujące wartości:

    * **Nazwa**: Wprowadź nazwę konta KCD.

    * **Sfera**: Wprowadź domenę i rozszerzenie wielką literą.

    * **Usługa SPN**: `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM`jest wymagana i musi być wielka litera. Przykład: `http/cweb2@IDENTT.WORK`.

    * **Użytkownik delegowany:** Wprowadź delegowaną nazwę użytkownika.

    * Zaznacz pole **wyboru Hasło dla delegowanego użytkownika,** a następnie wprowadź i potwierdź hasło.

1. Kliknij przycisk **OK**.
 
    ![Konfiguracja programu Citrix NetScaler — konfigurowanie okienka konta KCD](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Zasady ruchu i profil ruchu citrix

Aby skonfigurować zasady ruchu Citrix i profil ruchu:

1.  Przejdź do **security** > **AAA —** > **Zasady** > ruchu aplikacji**Zasady ruchu, profile i profile formularzy Profile SSOProfilne zasady**.

1.  Wybierz **profile ruchu**.

1.  Wybierz pozycję **Dodaj**.

1.  Aby skonfigurować profil ruchu, wprowadź lub wybierz następujące wartości.

    * **Nazwa**: Wprowadź nazwę profilu ruchu.

    * **Logowanie jednokrotne:** Wybierz **ON**.

    * **Konto KCD**: Wybierz konto KCD utworzone w poprzedniej sekcji.

1. Kliknij przycisk **OK**.

    ![Konfiguracja programu Citrix NetScaler — konfigurowanie okienka Profilu ruchu](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Wybierz pozycję **Zasady ruchu .**

1.  Wybierz pozycję **Dodaj**.

1.  Aby skonfigurować zasady ruchu drogowego, wprowadź lub wybierz następujące wartości:

    * **Nazwa**: Wprowadź nazwę zasad ruchu.

    * **Profil**: Wybierz profil ruchu utworzony w poprzedniej sekcji.

    * **Wyrażenie**: Wprowadź **wartość true**.

1. Kliknij przycisk **OK**.

    ![Konfiguracja programu Citrix NetScaler — konfigurowanie okienka Zasady ruchu](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Powiąż zasady ruchu z serwerem wirtualnym w citrix

Aby powiązać zasady ruchu z serwerem wirtualnym przy użyciu graficznego interfejsu użytkownika:

1. Przejdź do pozycji**Równoważenie** > obciążenia **zarządzania** > ruchem **.**

1. Na liście serwerów wirtualnych wybierz serwer wirtualny, z którym chcesz powiązać zasady przepisywania, a następnie wybierz pozycję **Otwórz**.

1. W okienku **Równoważenie obciążenia serwera wirtualnego** w obszarze **Ustawienia zaawansowane**wybierz pozycję **Zasady**. Wszystkie zasady skonfigurowane dla wystąpienia programu NetScaler są wyświetlane na liście.
 
    ![Konfiguracja programu Citrix NetScaler — okienko Równoważenie obciążenia serwera wirtualnego](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Konfiguracja programu Citrix NetScaler — okno dialogowe Zasady](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Zaznacz pole wyboru obok nazwy zasad, które mają być powiązane z tym serwerem wirtualnym.
 
    ![Konfiguracja programu Citrix NetScaler — okienko powiązania zasad ruchu serwera wirtualnego równoważenia obciążenia](./media/citrix-netscaler-tutorial/kerberos09.png)

1. W oknie dialogowym **Wybieranie typu:**

    1. W obszarze **Wybierz pozycję Wybierz zasady**wybierz pozycję **Ruch**.

    1. W obszarze **Wybierz typ**wybierz pozycję **Żądanie**.

    ![Konfiguracja programu Citrix NetScaler — wybieranie okienka Typ](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Gdy zasada jest powiązana, wybierz opcję **Gotowe**.
 
    ![Konfiguracja programu Citrix NetScaler — okienko Zasady](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Przetestuj powiązanie za pomocą witryny sieci Web WIA.

    ![Konfiguracja Citrix NetScaler — strona testowa w przeglądarce internetowej](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Tworzenie użytkownika testowego Citrix NetScaler

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Citrix NetScaler. Citrix NetScaler obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej, która jest domyślnie włączona. W tej sekcji nie ma żadnych działań do podjęcia. Jeśli użytkownik jeszcze nie istnieje w Citrix NetScaler, nowy jest tworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Test SSO 

W tej sekcji można przetestować konfigurację usługi Azure AD SSO przy użyciu panelu dostępu.

Po wybraniu kafelka Citrix NetScaler w Panelu dostępu należy zalogować się automatycznie do citrix NetScaler, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Citrix NetScaler z usługą Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie logowania jednokrotnego programu Citrix NetScaler w celu uwierzytelniania opartego na nagłówku](header-citrix-netscaler-tutorial.md)
