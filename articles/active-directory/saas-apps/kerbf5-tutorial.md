---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z f5 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f06b0b5aa59328d2fe39d501cfdf3ad7524427
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75431469"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z F5

W tym samouczku dowiesz się, jak zintegrować F5 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu F5 z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do F5.
* Włącz użytkownikom automatyczne logowanie do F5 za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

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

2. Na stronie **Konfiguracja z asystą** kliknij pozycję **Uaktualnij konfigurację z asystą** w lewym górnym rogu.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure14.png) 

3. Na ekranie pop Konfiguracji przewodnika po uaktualnieniu wybierz pozycję **Wybierz plik,** aby przekazać pobrany pakiet przypadków użycia, a następnie kliknij przycisk **Przekaż i zainstaluj.**

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure15.png) 

4. Po zakończeniu uaktualniania kliknij przycisk **Kontynuuj.**

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* F5 obsługuje **sp i idp** zainicjowane SSO
* F5 SSO można skonfigurować na trzy różne sposoby.

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenariusze uwierzytelniania kluczy

Oprócz obsługi integracji natywnej usługi Azure Active Directory dla nowoczesnych protokołów uwierzytelniania, takich jak Open ID Connect, SAML i WS-Fed, F5 rozszerza bezpieczny dostęp dla starszych aplikacji uwierzytelniania zarówno dla dostępu wewnętrznego, jak i zewnętrznego za pomocą usługi Azure AD, włączając nowoczesne scenariusze (np. dostęp bez hasła) do tych aplikacji. Należą do nich:

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

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź adres XML i certyfikat **metadanych federacji** **(Base64),** a następnie wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

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

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos

### <a name="guided-configuration"></a>Konfiguracja z przewodnikiem

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy F5 (Kerberos) jako administrator i wykonaj następujące czynności:

1. Należy zaimportować certyfikat metadanych do F5, który będzie używany w dalszej części procesu instalacji.

1. Przejdź do **pozycji Zarządzanie certyfikatami > systemowymi > zarządzanie certyfikatami ruchu > listę certyfikatów SSL**. Wybierz **pozycję Importuj** z prawego rogu. Określ **nazwę certyfikatu** (będzie się odwoływać później w konfiguracji). W **źródle certyfikatu**wybierz pozycję Przekaż plik określ certyfikat pobrany z platformy Azure podczas konfigurowania logowania jednokrotnego SAML. Kliknij pozycję **Importuj**.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure01.png) 

1. Ponadto będzie wymagany **certyfikat SSL dla nazwy hosta aplikacji. Przejdź do pozycji Zarządzanie certyfikatami > > > zarządzanie certyfikatami ruchu > listą certyfikatów SSL**. Wybierz **pozycję Importuj** z prawego rogu. **Typem importu** będzie **PKCS 12(IIS).** Określ **nazwę klucza** (będzie się odwoływać później w konfiguracji) i określ plik PFX. Określ **hasło** dla PFX. Kliknij pozycję **Importuj**.

    >[!NOTE]
    >W przykładzie nasza `Kerbapp.superdemo.live`nazwa aplikacji jest , używamy certyfikatu dzikiej karty nasza nazwa`WildCard-SuperDemo.live`

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure02.png) 
 
1. Firma Microsoft użyje środowiska z przewodnikiem do skonfigurowania federacji usługi Azure AD i dostępu do aplikacji. Przejdź do — F5 BIG-IP **Main** i wybierz **opcję > Konfiguracja z przewodnikiem > Federation > SAML.** Kliknij **przycisk Dalej,** a następnie kliknij przycisk **Dalej,** aby rozpocząć konfigurację.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure03.png) 

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure04.png)

1. Podaj **nazwę konfiguracji**. Określ **identyfikator jednostki** (taki sam, jak skonfigurowano w konfiguracji aplikacji usługi Azure AD). Określ **nazwę hosta**. Dodaj **opis** w celach informacyjnych. Zaakceptuj pozostałe wpisy domyślne i wybierz, a następnie kliknij przycisk **Zapisz & Dalej**.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure05.png) 

1. W tym przykładzie tworzymy nowy serwer wirtualny jako 192.168.30.200 z portem 443. Określ adres IP serwera wirtualnego w **adresie docelowym**. Wybierz **profil SSL**klienta , wybierz pozycję Utwórz nowy. Określ wcześniej przekazany certyfikat aplikacji (certyfikat dzikiej karty w tym przykładzie) i skojarzony klucz, a następnie kliknij przycisk **Zapisz & Dalej**.

    >[!NOTE]
    >w tym przykładzie nasz wewnętrzny serwer internetowy działa na porcie 80 i chcemy opublikować go z 443.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure06.png)

1. W obszarze **Wybierz metodę konfigurowania łącznika IdP**określ metadane, kliknij pozycję Wybierz plik i przekaż plik XML metadanych pobrany wcześniej z usługi Azure AD. Określ unikatową **nazwę** łącznika SAML IDP. Wybierz **certyfikat podpisywania metadanych,** który został przekazany wcześniej. Kliknij **przycisk Zapisz & Dalej**.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure07.png)  

1. W obszarze **Wybierz pulę**określ **pozycję Utwórz nowy** (wybierz pulę, która już istnieje). Niech inna wartość będzie domyślna. W obszarze Serwery puli wpisz adres IP w obszarze **Adres IP/Nazwa węzła**. Określ **port**. Kliknij **przycisk Zapisz & Dalej**.
 
    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure08.png)

1. Na ekranie Ustawienia logowania jednokrotnego wybierz pozycję **Włącz logowanie jednokrotne**. W obszarze **Wybrany typ logowania jednokrotnego** wybierz pozycję **Kerberos**. Zastąp **session.saml.last.Identity** **z session.saml.last.attr.name.Identity** w obszarze **Źródło nazwy użytkownika** (ta zmienna ustawiona przy użyciu mapowania oświadczeń w usłudze Azure AD). Wybierz **pozycję Pokaż ustawienie zaawansowane**. W obszarze **Obszar Kerberos** wpisz nazwę domeny. W **obszarze Nazwa konta/ Hasło konta** określ konto delegowania APM i hasło. Określ adres IP kontrolera domeny w polu **KDC.** Kliknij **przycisk Zapisz & Dalej**.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure09.png)   

1. Na potrzeby niniejszych wskazówek pominiemy kontrole punktów końcowych.  Szczegółowe informacje można znaleźć w dokumentacji F5.  Na ekranie wybierz pozycję **Zapisz & Dalej**.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure10.png) 

1. Zaakceptuj wartości domyślne i kliknij przycisk **Zapisz & Dalej**. Szczegółowe informacje dotyczące ustawień zarządzania sesjami SAML można znaleźć w dokumentacji F5.


    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure11.png) 
 
1. Przejrzyj ekran podsumowania i wybierz **pozycję Deploy,** aby skonfigurować BIG-IP.
 
    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure12.png)

1. Po skonfigurowaniu aplikacji kliknij **przycisk Zakończ**.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

>[!NOTE]
>Aby uzyskać informacje, kliknij [tutaj](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Konfigurowanie serwera AAA usługi Active Directory

Serwer AAA usługi Active Directory jest skonfigurowany w Menedżerze zasad dostępu (APM) w taki sposób, aby określał kontrolery domeny i poświadczenia dla usług APM używane do uwierzytelniania użytkowników.

1.  Na karcie Główny kliknij pozycję **Zasady dostępu > serwery AAA > usługi Active Directory**. Zostanie otwarty ekran listy Serwery usługi Active Directory.

2.  Kliknij przycisk **Utwórz**. Zostanie otwarty ekran Właściwości nowego serwera.

3.  W polu **Nazwa** wpisz unikatową nazwę serwera uwierzytelniania.

4.  W polu **Nazwa domeny** wpisz nazwę domeny systemu Windows.

5.  W przypadku ustawienia **Połączenie z serwerem** wybierz jedną z następujących opcji:

    * Wybierz **opcję Użyj puli,** aby skonfigurować wysoką dostępność dla serwera AAA.

    * Wybierz **opcję Bezpośredni,** aby skonfigurować serwer AAA dla funkcji autonomicznych.

6.  Jeśli wybrano opcję **Bezpośrednia,** wpisz nazwę w polu **Kontroler domeny.**

7.  Jeśli wybrano opcję Użyj **puli,** skonfiguruj pulę:

    * Wpisz nazwę w polu **Nazwa puli kontrolera domeny.**

    * Określ **kontrolery domeny** w puli, wpisując adres IP i nazwę hosta dla każdego z nich i klikając przycisk **Dodaj.**

    * Aby monitorować kondycję serwera AAA, masz możliwość wybrania monitora kondycji: w tym przypadku odpowiedni jest tylko monitor **gateway_icmp;** można go wybrać z listy **Monitor puli serwerów.**

8.  W polu **Nazwa administratora** wpisz nazwę z uwzględnieniem wielkości liter dla administratora, który ma uprawnienia administracyjne usługi Active Directory. Program APM używa informacji w polach **Nazwa administratora** i **Hasło administratora** dla kwerendy AD. Jeśli usługa Active Directory jest skonfigurowana dla kwerend anonimowych, nie trzeba podawać nazwy administratora. W przeciwnym razie usługa APM potrzebuje konta z wystarczającymi uprawnieniami do powiązania z serwerem usługi Active Directory, pobrania informacji o grupie użytkowników i pobrania zasad haseł usługi Active Directory w celu obsługi funkcji związanych z hasłem. (APM musi pobrać zasady haseł, na przykład, jeśli wybierzesz opcję Monituj użytkownika, aby zmienić hasło przed wygaśnięciem w akcji kwerendy usługi AD). Jeśli nie podasz informacji o koncie administratora w tej konfiguracji, APM używa konta użytkownika do pobierania informacji. Działa to, jeśli konto użytkownika ma wystarczające uprawnienia.

9.  W polu **Hasło administratora** wpisz hasło administratora skojarzone z nazwą domeny.

10. W polu **Weryfikuj hasło administratora** wpisz ponownie hasło administratora skojarzone z ustawieniem **Nazwa domeny.**

11. W polu **Okres istnienia pamięci podręcznej grupy** wpisz liczbę dni. Domyślny okres istnienia wynosi 30 dni.

12. W polu **Okres istnienia pamięci podręcznej obiektów zabezpieczeń haseł** wpisz liczbę dni. Domyślny okres istnienia wynosi 30 dni.

13. Z listy **Typ szyfrowania wstępnego protokołu Kerberos** wybierz typ szyfrowania. Wartość domyślna to **Brak**. Jeśli określisz typ szyfrowania, system BIG-IP zawiera dane preauthentication protokołu Kerberos w pakiecie pierwszego żądania usługi uwierzytelniania (AS-REQ).

14. W polu **Limit czasu** wpisz interwał limitu czasu (w sekundach) dla serwera AAA. (To ustawienie jest opcjonalne).

15. Kliknij pozycję **Zakończone**. Nowy serwer jest wyświetlany na liście. Spowoduje to dodanie nowego serwera usługi Active Directory do listy Serwery usługi Active Directory.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>Konfiguracja SAML

1. Należy zaimportować certyfikat metadanych do F5, który będzie używany w dalszej części procesu instalacji. Przejdź do **pozycji Zarządzanie certyfikatami > systemowymi > zarządzanie certyfikatami ruchu > listę certyfikatów SSL**. Wybierz **pozycję Importuj** z prawego rogu.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure18.png)

2. Aby skonfigurować dostawcę tożsamości SAML, **przejdź do pozycji > > > > SAML: Usługodawca > zewnętrzne łączniki Idp**i kliknij przycisk **Utwórz > z metadanych**.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure19.png)

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure20.png)

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure21.png)

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure22.png)

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure23.png)

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure24.png)

1. Aby skonfigurować dodatek SAML SP, przejdź do **usługi Access > Federation > SAML Service Provider > Local SP Services** i kliknij przycisk **Utwórz**. Uzupełnij następujące informacje i kliknij przycisk **OK**.

    * Nazwa typu: KerbApp200SAML
    * Identyfikator jednostki*:https://kerbapp200.superdemo.live
    * Ustawienia nazwy SP
    * Schemat: https
    * Gospodarz: kerbapp200.superdemo.live
    * Opis: kerbapp200.superdemo.live

     ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure25.png)

     b. Wybierz opcję Konfiguracja SP, KerbApp200SAML i kliknij pozycję **Bind/UnBind IdP Connectors**.

     ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure26.png)

     ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure27.png)

     d. Kliknij pozycję **Dodaj nowy wiersz** i wybierz łącznik **Zewnętrzny IdP** utworzony w poprzednim kroku, kliknij pozycję **Aktualizuj**, a następnie kliknij przycisk **OK**.

     ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure28.png)

1. Aby skonfigurować protokół SSO protokołu Kerberos, przejdź do **pozycji Dostęp > logowania jednokrotnego > protokołu Kerberos**, pełne informacje i kliknij przycisk **Zakończone**.

    >[!Note]
    > Konto delegowania Protokołu Kerberos będzie potrzebne do utworzenia i określenia. Zapoznaj się z sekcją KCD (zapoznaj się z dodatkiem do odniesień do zmiennych)

    * **Źródło nazwy użytkownika:** session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Źródło obszaru użytkownika:** session.logn.last.domain

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure29.png)

1. Aby skonfigurować profil programu Access, przejdź do **pozycji Profil/Zasady > programu Access > profil dostępu (na zasady sesji),** kliknij przycisk **Utwórz, uzupełnij**następujące informacje i kliknij przycisk **Zakończone**.

    * Nazwa: KerbApp200
    * Typ profilu: Wszystkie
    * Zakres profilu: Profil
    * Języki: angielski

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure30.png)

1. Kliknij nazwę KerbApp200, uzupełnij następujące informacje i kliknij **przycisk Aktualizuj**.

    * Plik cookie domeny: superdemo.live
    * Konfiguracja sytomów: KerAppSSO_sso

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure31.png)

1. Kliknij **pozycję Zasady dostępu,** a następnie kliknij pozycję **Edytuj zasady dostępu** dla profilu "KerbApp200".

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure32.png)

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure33.png)

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure34.png)

    * **session.logn.last.usernameUPN expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain TEKST superdemo.live**

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logn.last.usernameUPN})**

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure36.png)

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure37.png)

    * **session.logn.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } }**

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.loginn.last.username}**
    * **mcget {session.logn.last.password**

1. Aby dodać nowy węzeł, przejdź do **pozycji Ruch lokalny > węzły > listę węzłów, kliknij przycisk Utwórz,** uzupełnij następujące informacje, a następnie kliknij przycisk **Zakończone**.

    * Nazwa: KerbApp200
    * Opis: KerbApp200
    * Adres: 192.168.20.200

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure39.png)

1. Aby utworzyć nową pulę, przejdź do pozycji **Ruch lokalny > Puly > lista puli, kliknij przycisk Utwórz,** uzupełnij następujące informacje i kliknij przycisk **Zakończone**.

    * Nazwa: KerbApp200-Pool
    * Opis: KerbApp200-Pool
    * Monitory zdrowia: http
    * Adres: 192.168.20.200
    * Port serwisowy: 81

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure40.png)

1. Aby utworzyć serwer wirtualny, przejdź do **pozycji Ruch lokalny > serwery wirtualne > lista serwerów wirtualnych > +**, uzupełnij następujące informacje i kliknij przycisk **Zakończone**.

    * Nazwa: KerbApp200
    * Adres docelowy/maska: Host 192.168.30.200
    * Port usługi: Port 443 HTTPS
    * Profil dostępu: KerbApp200
    * Określanie profilu programu Access utworzonego w poprzednim kroku

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure41.png)

        ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Konfigurowanie delegowania protokołu Kerberos 

>[!NOTE]
>Aby uzyskać informacje, kliknij [tutaj](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Krok 1:** Tworzenie konta delegowania

    **Przykład:**
    * Nazwa domeny: **superdemo.live**

    * Sam Nazwa konta: **big-ipuser**

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Krok 2:** Ustawianie nazwy SPN (na koncie delegowania APM)

    **Przykład:**
    * setspn **-Host/big-ipuser.superdemo.live** big-ipuser

* **Krok 3:** Delegowanie nazwy SPN (dla konta usługi app service) Skonfiguruj odpowiednie delegowanie dla konta delegowania F5.
    W poniższym przykładzie konto delegowania APM jest konfigurowane dla KCD dla FRP-App1.superdemo. aplikacji na żywo.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Podaj szczegóły wymienione w powyższym dokumencie referencyjnym w [niniejszym](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)dokumencie .

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

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos](advance-kerbf5-tutorial.md)

