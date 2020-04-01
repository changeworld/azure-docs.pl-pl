---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z f5 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 352f52a2a6d84d352bb46e09f104efde303307f5
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z F5

W tym samouczku dowiesz się, jak zintegrować F5 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu F5 z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do F5.
* Włącz użytkownikom automatyczne logowanie do F5 za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z logiem jednokrotnym w usłudze Azure AD, zobacz [Logowanie jednokrotne w aplikacjach usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)

* F5 rejestracji jednokrotnej (SSO) włączone subskrypcji.

* Wdrożenie wspólnego rozwiązania wymaga następującej licencji:

    * F5 BIG-IP® Najlepszy pakiet (lub) 

    * Licencja autonomiczna F5 BIG-IP Access Policy Manager™ (APM) 

    * F5 BIG-IP Access Policy Manager™ (APM) licencja dodatkowa na istniejącym BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM).

    * Oprócz powyższej licencji, system F5 może być również licencjonowany z: 

        * Subskrypcja filtrowania adresów URL w celu użycia bazy danych kategorii adresu URL

        * Subskrypcja analizy IP F5 służąca do wykrywania i blokowania znanych napastników i złośliwego ruchu
     
        * Sieciowy sprzętowy moduł zabezpieczeń (HSM) do ochrony kluczy cyfrowych i zarządzania nimi w celu silnego uwierzytelniania

* System F5 BIG-IP jest aprowiony modułami APM (LTM jest opcjonalny)

* Chociaż jest to opcjonalne, zdecydowanie zaleca się wdrażanie systemów F5 w [grupie urządzeń synchronizacji/pracy awaryjnej](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), która obejmuje aktywną parę wstrzymania z pływającym adresem IP dla wysokiej dostępności (HA). Dalszą nadmiarowość interfejsu można osiągnąć za pomocą protokołu LACP (Link Aggregation Control Protocol). Lacp zarządza połączonymi interfejsami fizycznymi jako pojedynczy interfejs wirtualny (grupa agregująca) i wykrywa wszelkie błędy interfejsu w grupie.

* W przypadku aplikacji Protokołu Kerberos lokalne konto usługi AD dla ograniczonego delegowania.  Aby utworzyć konto delegowania usługi AD, zapoznaj się z [dokumentacją F5.](https://support.f5.com/csp/article/K43063049)

## <a name="access-guided-configuration"></a>Konfiguracja z przewodnikiem dostępu

* Konfiguracja z przewodnikiem dostępu" jest obsługiwana w wersji 13.1.0.8 F5 TMOS lub wyższej. Jeśli w systemie BIG-IP jest uruchomiona wersja poniżej 13.1.0.8, zapoznaj się z sekcją **Konfiguracja zaawansowana.**

* Konfiguracja z przewodnikiem dostępu przedstawia zupełnie nowe i usprawnione środowisko użytkownika. Ta architektura oparta na przepływie pracy zapewnia intuicyjne, ponowne wykonanie kroków konfiguracji dostosowanych do wybranej topologii.

* Przed przejściem do konfiguracji uaktualnić konfigurację z przewodnikiem, pobierając najnowszy pakiet przypadków użycia z [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Aby uaktualnić, wykonaj poniższą procedurę.

    >[!NOTE]
    >Poniższe zrzuty ekranu są dla najnowszej wersji wydanej (BIG-IP 15.0 z AGC w wersji 5.0). Poniższe kroki konfiguracji są prawidłowe dla tego przypadku użycia w poprzek od 13.1.0.8 do najnowszej wersji BIG-IP.

1. W interfejsie internetowym F5 BIG-IP kliknij przycisk **Konfiguracja przewodnika >> programu Access**.

1. Na stronie **Konfiguracja z asystą** kliknij pozycję **Uaktualnij konfigurację z asystą** w lewym górnym rogu.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure14.png) 

1. Na ekranie pop Konfiguracji przewodnika po uaktualnieniu wybierz pozycję **Wybierz plik,** aby przekazać pobrany pakiet przypadków użycia, a następnie kliknij przycisk **Przekaż i zainstaluj.**

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure15.png) 

1. Po zakończeniu uaktualniania kliknij przycisk **Kontynuuj.**

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* F5 SSO można skonfigurować na trzy różne sposoby.

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenariusze uwierzytelniania kluczy

* Oprócz obsługi integracji natywnej usługi Azure Active Directory dla nowoczesnych protokołów uwierzytelniania, takich jak Open ID Connect, SAML i WS-Fed, F5 rozszerza bezpieczny dostęp dla starszych aplikacji uwierzytelniania dla dostępu wewnętrznego i zewnętrznego za pomocą usługi Azure AD, umożliwiając nowoczesne scenariusze (np. dostęp bez hasła) do tych aplikacji. Należą do nich:

* Aplikacje uwierzytelniania oparte na nagłówku

* Aplikacje uwierzytelniania Kerberos

* Uwierzytelnianie anonimowe lub brak wbudowanych aplikacji uwierzytelniania

* Aplikacje uwierzytelniania NTLM (ochrona z dwoma monitami dla użytkownika)

* Aplikacja oparta na formularzach (ochrona z dwoma monitami dla użytkownika)

## <a name="adding-f5-from-the-gallery"></a>Dodawanie F5 z galerii

Aby skonfigurować integrację F5 z usługą Azure AD, należy dodać F5 z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **klawisz F5** w polu wyszukiwania.
1. Wybierz **klawisz F5** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla F5

Konfigurowanie i testowanie usługi Azure AD SSO z F5 przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w F5.

Aby skonfigurować i przetestować syg jako jeden na wirtualną usługę Azure ad za pomocą f5, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne F5](#configure-f5-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego F5](#create-f5-test-user)** — aby mieć odpowiednik B.Simon w F5, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **F5** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<YourCustomFQDN>.f5.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta F5,](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod XML i certyfikat **metadanych federacji** **(Base64)** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie F5** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do F5.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **F5**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**
1. Kliknij opcję **Dostęp warunkowy** .
1. Kliknij **nowe zasady**.
1. Teraz możesz zobaczyć aplikację F5 jako zasób dla zasad urzędu certyfikacji i zastosować dowolny dostęp warunkowy, w tym wielofactor Auth, Kontrola dostępu oparta na urządzeniach lub zasady ochrony tożsamości.

## <a name="configure-f5-sso"></a>Konfigurowanie sytego syda dla F5

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku

### <a name="guided-configuration"></a>Konfiguracja z przewodnikiem

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy F5 (opartej na nagłówku) jako administrator i wykonaj następujące czynności:

1. Przejdź do **pozycji Zarządzanie certyfikatami > systemowymi > zarządzanie certyfikatami ruchu > listę certyfikatów SSL**. Wybierz **pozycję Importuj** z prawego rogu. Określ **nazwę certyfikatu** (będzie się odwoływać później w konfiguracji). W **źródle certyfikatu**wybierz pozycję Przekaż plik określ certyfikat pobrany z platformy Azure podczas konfigurowania logowania jednokrotnego SAML. Kliknij pozycję **Importuj**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure12.png)
 
1. Ponadto będzie wymagany **certyfikat SSL dla nazwy hosta aplikacji. Przejdź do pozycji Zarządzanie certyfikatami > > > zarządzanie certyfikatami ruchu > listą certyfikatów SSL**. Wybierz **pozycję Importuj** z prawego rogu. **Typem importu** będzie **PKCS 12(IIS).** Określ **nazwę klucza** (będzie się odwoływać później w konfiguracji) i określ plik PFX. Określ **hasło** dla PFX. Kliknij pozycję **Importuj**.

    >[!NOTE]
    >W przykładzie nasza `Headerapp.superdemo.live`nazwa aplikacji jest , używamy certyfikatu `WildCard-SuperDemo.live`dzikiej karty nasza nazwa kluczowa jest .

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure13.png)

1. Firma Microsoft użyje środowiska z przewodnikiem do skonfigurowania federacji usługi Azure AD i dostępu do aplikacji. Przejdź do — F5 BIG-IP **Main** i wybierz **opcję > Konfiguracja z przewodnikiem > Federation > SAML.** Kliknij **przycisk Dalej,** a następnie kliknij przycisk **Dalej,** aby rozpocząć konfigurację.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure01.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure02.png)
 
1. Podaj **nazwę konfiguracji**. Określ **identyfikator jednostki** (taki sam, jak skonfigurowano w konfiguracji aplikacji usługi Azure AD). Określ **nazwę hosta**. Dodaj **opis** w celach informacyjnych. Zaakceptuj pozostałe wpisy domyślne i wybierz, a następnie kliknij przycisk **Zapisz & Dalej**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure03.png) 

1. W tym przykładzie tworzymy nowy serwer wirtualny jako 192.168.30.20 z portem 443. Określ adres IP serwera wirtualnego w **adresie docelowym**. Wybierz **profil SSL**klienta , wybierz pozycję Utwórz nowy. Określ wcześniej przekazany certyfikat aplikacji (certyfikat dzikiej karty w tym przykładzie) i skojarzony klucz, a następnie kliknij przycisk **Zapisz & Dalej**.

    >[!NOTE]
    >w tym przykładzie nasz wewnętrzny serwer internetowy działa na porcie 888 i chcemy opublikować go z 443.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure04.png) 

1. W obszarze **Wybierz metodę konfigurowania łącznika IdP**określ metadane, kliknij pozycję Wybierz plik i przekaż plik XML metadanych pobrany wcześniej z usługi Azure AD. Określ unikatową **nazwę** łącznika SAML IDP. Wybierz **certyfikat podpisywania metadanych,** który został przekazany wcześniej. Kliknij **przycisk Zapisz & Dalej**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure05.png)
 
1. W obszarze **Wybierz pulę**określ **pozycję Utwórz nowy** (wybierz pulę, która już istnieje). Niech inna wartość będzie domyślna. W obszarze Serwery puli wpisz adres IP w obszarze **Adres IP/Nazwa węzła**. Określ **port**. Kliknij **przycisk Zapisz & Dalej**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure06.png)

1. Na ekranie Ustawienia logowania jednokrotnego wybierz pozycję **Włącz logowanie jednokrotne**. W obszarze Wybrany typ logowania jednokrotnego wybierz **pozycję Http na podstawie nagłówka**. Zastąp **session.saml.last.Identity** **z session.saml.last.attr.name.Identity** w obszarze Źródło nazwy użytkownika (ta zmienna ustawiona przy użyciu mapowania oświadczeń w usłudze Azure AD). W obszarze Nagłówki SYC.

    * **Nazwa nagłówka : MyAuthorization**

    * **Wartość nagłówka: %{session.saml.last.attr.name.Identity}**

    * Kliknij **przycisk Zapisz & Dalej**

    Pełną listę zmiennych i wartości można znaleźć w dodatku. W razie potrzeby można dodać więcej nagłówków.

    >[!NOTE]
    >Nazwa konta to utworzone konto delegowania F5 (sprawdź dokumentację F5).

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure07.png) 

1. Na potrzeby niniejszych wskazówek pominiemy kontrole punktów końcowych.  Szczegółowe informacje można znaleźć w dokumentacji F5. Wybierz **pozycję Zapisz & Dalej**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure08.png)

1. Zaakceptuj wartości domyślne i kliknij przycisk **Zapisz & Dalej**. Szczegółowe informacje dotyczące ustawień zarządzania sesjami SAML można znaleźć w dokumentacji F5.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure09.png)

1. Przejrzyj ekran podsumowania i wybierz **pozycję Deploy,** aby skonfigurować BIG-IP. kliknij **przycisk Zakończ**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure10.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Ta sekcja jest przeznaczona do użycia, jeśli nie można użyć konfiguracji z przewodnikiem lub chcesz dodać/zmodyfikować dodatkowe parametry. Dla nazwy hosta aplikacji wymagany jest certyfikat TLS/SSL.

1. Przejdź do **pozycji Zarządzanie certyfikatami > systemowymi > zarządzanie certyfikatami ruchu > listę certyfikatów SSL**. Wybierz **pozycję Importuj** z prawego rogu. **Typem importu** będzie **PKCS 12(IIS).** Określ **nazwę klucza** (będzie się odwoływać później w konfiguracji) i określ plik PFX. Określ **hasło** dla PFX. Kliknij pozycję **Importuj**.

    >[!NOTE]
    >W przykładzie nasza `Headerapp.superdemo.live`nazwa aplikacji jest , używamy certyfikatu `WildCard-SuperDemo.live`dzikiej karty nasza nazwa kluczowa jest .
  
    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Dodawanie nowego serwera sieci Web do bigip-F5

1. Kliknij **na Główne > IApps > Usługi aplikacji > > aplikacji .**

1. Podaj **nazwę** i w obszarze **Szablon** wybierz **f5.http**.
 
    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure18.png)

1. Opublikujemy nasz HeaderApp2 zewnętrznie jako HTTPS w tym przypadku, **w jaki sposób system BIG-IP powinien obsługiwać SSL Traffic?** określamy **Zakończ SSL z klienta, Zwykły tekst do serwerów (SSL Offload)**. Określ certyfikat i klucz w obszarze **Który certyfikat SSL chcesz użyć?** i **którego klucza prywatnego SSL chcesz użyć?**. Określ adres IP serwera wirtualnego w obszarze **Jaki adres IP ma być używany dla serwera wirtualnego?**. 

    * **Określ inne szczegóły**

        * Nazwa FQDN  

        * Określ wyjście z puli aplikacji lub utwórz nową.

        * Podczas tworzenia nowego serwera aplikacji określ **wewnętrzny adres IP** i numer **portu**.

        ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure19.png) 

1. Kliknij pozycję **Zakończone**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure20.png) 

1. Upewnij się, że właściwości aplikacji mogą być modyfikowane. Kliknij **opcję > główne IApps > usługi aplikacji: Aplikacje >> HeaderApp2**. Odznacz **ścisłe aktualizacje** (zmodyfikujemy niektóre ustawienia poza gui). Kliknij przycisk **Aktualizuj.**

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure21.png) 

1. W tym momencie powinieneś być w stanie przeglądać serwer wirtualny.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Konfigurowanie F5 jako SP i platformy Azure jako usługi IDP

1.  Kliknij **pozycję > Federacyjny programu Access> dostawca usług SAML > lokalna usługa SP > kliknij przycisk utwórz lub + podpisz**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure22.png)

1. Określ szczegóły usługi usługodawcy. Określ **nazwę** reprezentującą konfigurację sp F5. Określ **identyfikator jednostki** (zazwyczaj taki sam jak adres URL aplikacji).

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure23.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure24.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure25.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure26.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure27.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Tworzenie łącznika Idp

1. Kliknij przycisk **Bind/Unbind IdP Connectors,** wybierz pozycję **Utwórz nowy łącznik IdP** i wybierz opcję Z **metadanych,** a następnie wykonaj następujące czynności:
 
    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure29.png)

    a. Przejdź do pliku metadata.xml pobranego z usługi Azure AD i określ **nazwę dostawcy tożsamości**.

    b. Kliknij **przycisk ok**.

    d. Łącznik zostanie utworzony, a certyfikat jest gotowy automatycznie z pliku xml metadanych.
    
    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure30.png)

    d. Skonfiguruj F5BIG-IP, aby wysłać wszystkie żądania do usługi Azure AD.

    e. Kliknij **pozycję Dodaj nowy wiersz**, wybierz pozycję **AzureIDP** (zgodnie z poprzednimi krokami, określ 

    f. **Pasujące źródło = %{session.server.landinguri}** 

    g. **Wartość dopasowania = /***

    h. Kliknij **przycisk Aktualizuj**

    i. Kliknij **przycisk OK**

    j. **Konfiguracja SAML IDP została zakończona**
    
    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Konfigurowanie zasad F5 w celu przekierowania użytkowników do usługi Azure SAML IDP

1. Aby skonfigurować zasady F5 w celu przekierowania użytkowników do usługi Azure SAML IDP, wykonaj następujące kroki:

    a. Kliknij **pozycję Główny > dostęp > profil/zasady > profile dostępu**.

    b. Kliknij przycisk **Utwórz.**

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure32.png)
 
    d. Określ **nazwę** (HeaderAppAzureSAMLPolicy w przykładzie).

    d. Można dostosować inne ustawienia, zapoznaj się z dokumentacją F5.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure33.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure34.png) 

    e. Kliknij pozycję **Zakończone**.

    f. Po zakończeniu tworzenia zasad kliknij politykę i przejdź do karty **Zasady dostępu.**

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure35.png)
 
    g. Kliknij **edytor zasad wizualnych**, edytuj **łącze Zasady dostępu dla profilu.**

    h. Kliknij + Zaloguj się w edytorze zasad wizualnych i wybierz **SAML Auth**.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure36.png)

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure37.png)
 
    i. Kliknij **pozycję Dodaj element**.

    j. W obszarze **Właściwości** określ **nazwę** i w obszarze **Serwer AAA** wybierz poprzednio skonfigurowaną **sp.**
 
    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure38.png)

    k. Podstawowe zasady są gotowe, aby dostosować zasady do uwzględnienia dodatkowych magazynów źródeł/atrybutów.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure39.png)
 
    l. Upewnij się, że klikniesz link **Zastosuj zasady dostępu** u góry.

### <a name="apply-access-profile-to-the-virtual-server"></a>Stosowanie profilu dostępu do serwera wirtualnego

1. Przypisz profil dostępu do serwera wirtualnego, aby F5 BIG-IP APM zastosował ustawienia profilu do ruchu przychodzącego i uruchomić wcześniej zdefiniowane zasady dostępu.

    a. Kliknij **pozycję Główne** > **serwery wirtualne****ruchu** > lokalnego .

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure40.png)
 
    b. Kliknij na serwerze wirtualnym, przewiń do sekcji **Zasady dostępu,** w obszarze rozwijanego **Profil dostępu** i wybierz utworzone zasady SAML (w przykładzie HeaderAppAzureSAMLPolicy)

    d. Kliknij **przycisk Aktualizuj**
 
    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure41.png)

    d. utworzyć F5 BIG-IP iRule® wyodrębnić niestandardowe atrybuty SAML z potwierdzenia przychodzącego i przekazać je jako nagłówki HTTP do aplikacji testowej wewnętrznej bazy danych. Kliknij **pozycję Główny > ruch lokalny > iRules > listę iRule > kliknij przycisk Utwórz**

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure42.png)
 
    e. Wklej tekst F5 BIG-IP iRule poniżej w oknie Definicja.

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure43.png)
 
    gdy RULE_INIT { set static::debug 0 } podczas ACCESS_ACL_ALLOWED {

    ustaw AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } jeśli { !( [HTTP::header istnieje "AZUREAD_USERNAME"]) } { HTTP::wstaw nagłówek "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    ustaw AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name.http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } jeśli { !( [HTTP::header istnieje "AZUREAD_DISPLAYNAME"]) { HTTP::wstaw nagłówek "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    ustaw AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } jeśli { !( [HTTP::header istnieje "AZUREAD_EMAILADDRESS"]) { HTTP::wstaw nagłówek "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Przykładowe wyjście poniżej**

    ![Konfiguracja F5 (oparta na nagłówku)](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Tworzenie użytkownika testowego F5

W tej sekcji utworzysz użytkownika o nazwie B.Simon w F5. Współpracuj z [zespołem pomocy technicznej klienta F5,](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) aby dodać użytkowników na platformie F5. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka F5 w Panelu dostępu należy automatycznie zalogować się do F5, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj F5 z usługą Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos](advance-kerbf5-tutorial.md)

