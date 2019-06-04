---
title: Dodawanie aplikacji lokalnej — serwer proxy aplikacji w usłudze Azure Active Directory | Microsoft Docs
description: Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. W tym samouczku przedstawiono sposób przygotowania środowiska do użytku z serwerem Proxy aplikacji. Następnie używa witryny Azure portal, aby dodać aplikację do dzierżawy usługi Azure AD w środowisku lokalnym.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82c7b698f655b82ba95f66127f27a921def02cde
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472997"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Samouczek: Dodawanie aplikacji lokalnej na potrzeby dostępu zdalnego za pomocą serwera proxy aplikacji w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. W tym samouczku przygotujemy środowisko do obsługi serwera proxy aplikacji. Gdy środowisko będzie gotowe, za pomocą witryny Azure Portal dodamy aplikację lokalną do dzierżawy usługi Azure AD.

Ten samouczek umożliwia opanowanie następujących czynności:

> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD
> * Sprawdza, czy użytkownika testowego można logowania do aplikacji przy użyciu konta usługi Azure AD

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby dodać aplikację do usługi Azure AD w środowisku lokalnym, należy:

* A [subskrypcji usługi Microsoft Azure AD podstawowa lub premium](https://azure.microsoft.com/pricing/details/active-directory)
* Konto administratora aplikacji
* Tożsamości użytkowników musi być synchronizowane z katalogu lokalnego lub utworzonych bezpośrednio w dzierżawcy usługi Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD do wstępnie uwierzytelnia użytkowników, zanim nadawania im praw do serwera Proxy aplikacji opublikowane aplikacje i udostępniają informacje identyfikator użytkownika wymagane do wykonywania logowania jednokrotnego (SSO).

### <a name="windows-server"></a>Windows server

Aby użyć serwera proxy aplikacji, potrzebujesz serwera systemu Windows z systemem Windows Server 2012 R2 lub nowszym. Na serwerze zainstalujesz łącznik serwera proxy aplikacji. Serwer łącznika musi się łączyć z usługami serwera proxy aplikacji na platformie Azure oraz aplikacjami lokalnymi, które zamierzasz opublikować.

Aby zapewnić wysoką dostępność w środowisku produkcyjnym, zalecamy użycie więcej niż jednego serwera systemu Windows. W tym samouczku wystarczy nam jeden serwer systemu Windows.

#### <a name="recommendations-for-the-connector-server"></a>Zalecenia dotyczące serwera łącznika

1. Serwer łącznika powinien znajdować się blisko serwerów aplikacji, aby wydajność komunikacji między łącznikiem a aplikacją była optymalna. Aby uzyskać więcej informacji, zobacz [Network topology considerations (Kwestie związane z topologią sieci)](application-proxy-network-topology.md).

2. Serwer łącznika i serwerów aplikacji sieci web powinna należeć do tej samej domeny usługi Active Directory lub span domenach ufających. Posiadających serwery w tej samej domenie lub domenach ufających jest wymaganie dotyczące korzystania z logowania jednokrotnego (SSO) przy użyciu zintegrowanego Windows Authentication (Zintegrowane) i delegowanie ograniczone protokołu Kerberos (KCD). Jeśli serwer łącznika znajduje się w innej domenie usługi Active Directory niż serwery aplikacji internetowych, korzystanie z logowania jednokrotnego wymaga użycia delegowania opartego na zasobach. Aby uzyskać więcej informacji, zobacz [KCD for single sign-on with Application Proxy (Ograniczone delegowanie Kerberos dla logowania jednokrotnego przy użyciu serwera proxy aplikacji)](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="tls-requirements"></a>Wymaganiami dotyczącymi protokołu TLS

Przed zainstalowaniem łącznika serwera proxy aplikacji na serwerze łącznika systemu Windows należy włączyć protokół TLS 1.2.

Aby włączyć protokół TLS 1.2:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Uruchom ponownie serwer.

>[!Important] 
> Aby zapewnić najlepsze w swojej klasie szyfrowanie dla naszych klientów, firma Microsoft wprowadza aktualizacji do usługi serwera Proxy aplikacji, aby ograniczyć dostęp do tylko protokoły TLS 1.2. Oparte na gotowość klienta, które zmiany będzie stopniowo wdrażana do klientów, którzy są tylko przy użyciu protokołów TLS 1.2 i nie będą widzieć żadnego wpływu ta zmiana. Protokół TLS 1.0 i 1.1 wycofywania zakończy się na 31 sierpnia 2019 r, a klienci otrzymają wyprzedzeniem, aby przygotować się do tej zmiany. Aby przygotować się do tej zmiany upewnij się, że, wszystkie kombinacje klient serwer i serwer przeglądarki są aktualizowane zachować połączenie z usługą serwera Proxy aplikacji za pomocą protokołu TLS 1.2. Dotyczy to klientów, których użytkownicy korzystają z dostępu do aplikacji opublikowanych przez serwer Proxy aplikacji. Zobacz Przygotowanie do [protokołu TLS 1.2 w usłudze Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) przydatne dane i zasoby.

## <a name="prepare-your-on-premises-environment"></a>Przygotowywanie środowiska lokalnego

Rozpocznij, należy włączyć komunikację z centrów danych platformy Azure, aby przygotować środowisko dla serwera Proxy aplikacji usługi Azure AD. Jeśli ścieżka znajduje się zapora, upewnij się, że jest on otwarty. Otwórz zapora zezwala na łącznika na wysyłanie żądań protokołu HTTPS (TCP) do serwera Proxy aplikacji.

### <a name="open-ports"></a>Otwieranie portów

Otwórz następujące porty dla ruchu **wychodzącego**. 

   | Numer portu | Zastosowanie |
   | --- | --- |
   | 80 | Pobieranie list odwołania certyfikatów oraz weryfikowanie certyfikatu SSL |
   | 443 | Cała komunikacja wychodząca do usługi serwera proxy aplikacji |

Jeśli zapora wymusza ruch odpowiednio dla użytkowników, którzy go generują, otwórz również porty 80 i 443 dla ruchu pochodzącego z usług systemu Windows działających jako usługi sieciowe.

Masz starszą wersję łącznika zainstalowane, jeśli już używasz serwera Proxy aplikacji. Aby zainstalować najnowszą wersję łącznika, wykonaj czynności opisane w tym samouczku. Wersje starsze niż 1.5.132.0 wymagają również otwarcia następujących portów: 5671, 8080, 9090–9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL

Zezwól na dostęp do następujących adresów URL:

| URL | Zastosowanie |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Platforma Azure używa tych adresów URL, aby zweryfikować certyfikaty. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Łącznik używa tych adresów URL podczas procesu rejestracji. |

Można zezwolić na połączenia z \*. msappproxy.net i \*. servicebus.windows.net, jeśli zapora lub serwer proxy umożliwia konfigurowanie systemu DNS umożliwia listy. W przeciwnym razie musisz zezwolić na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Zakresy adresów IP są aktualizowane co tydzień.

## <a name="install-and-register-a-connector"></a>Instalowanie i rejestrowanie łącznika

Aby użyć serwera Proxy aplikacji, należy zainstalować łącznik na każdym serwerze Windows, który jest używany z usługą Application Proxy. Łącznik to agent, który zarządza połączeniami wychodzącymi z lokalnych serwerów aplikacji do serwera proxy aplikacji w usłudze Azure AD. Łącznik można zainstalować na serwerach, na których zainstalowano również innych agentów uwierzytelniania, takich jak program Azure AD Connect.

Aby zainstalować łącznik:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator aplikacji w katalogu, w którym jest używany serwer proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias administratora w tej domenie.
2. Wybierz swoją nazwę użytkownika w prawym górnym rogu. Sprawdź, czy zalogowano do katalogu, który używa serwera Proxy aplikacji. Jeśli potrzebujesz zmienić katalogi, wybierz **Przełącz katalog** i wybierz katalog, który używa serwera Proxy aplikacji.
3. W panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**. 
4. W obszarze **Zarządzaj**, wybierz opcję **serwera proxy aplikacji**.
5. Wybierz **Pobierz usługę łącznika**.
    
    ![Pobierz usługę łącznika](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

6. Zapoznaj się z warunkami użytkowania usługi.  Gdy wszystko będzie gotowe, wybierz **Zaakceptuj warunki i Pobierz**.
7. W dolnej części okna wybierz **Uruchom** instalacji łącznika. Zostanie otwarty kreator instalacji. 
8. Postępuj zgodnie z instrukcjami w kreatorze, aby zainstalować usługę. Po wyświetleniu monitu o zarejestrowanie łącznika na serwerze proxy aplikacji w dzierżawie usługi Azure AD, wprowadź swoje poświadczenia administratora aplikacji.
    - Jeśli korzystasz z programu Internet Explorer (IE) i opcja **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** ma wartość **Wł**, ekran rejestracji może nie być widoczny. Aby uzyskać dostęp, wykonaj instrukcje podane w komunikacie o błędzie. Upewnij się, że **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** ustawiono **poza**.

### <a name="general-remarks"></a>Uwagi ogólne

Jeśli wcześniej zainstalowano łącznik, zainstaluj go ponownie, aby uzyskać najnowszą wersję. Aby wyświetlić informacje o wersjach wcześniej wydanych i jakich zmian, ich zawierają, zobacz [serwera Proxy aplikacji: Historia wersji](application-proxy-release-version-history.md).

Jeśli masz więcej niż jeden serwer systemu Windows dla aplikacji lokalnych, musisz zainstalować i zarejestrować łącznik na każdym serwerze. Możesz tworzyć grupy łączników. Aby uzyskać więcej informacji, zobacz [Grupy łączników](application-proxy-connector-groups.md). 

Jeśli do łączenia się z Internetem w Twojej organizacji są używane serwery proxy, musisz je skonfigurować pod kątem serwera proxy aplikacji.  Aby uzyskać więcej informacji, zobacz [Work with existing on-premises proxy servers (Praca z istniejącymi lokalnymi serwerami proxy)](application-proxy-configure-connectors-with-proxy-servers.md). 

Aby uzyskać informacje o łącznikach oraz ich aktualizowaniu, a także wskazówki na temat planowania wydajności, zobacz [Understand Azure AD Application Proxy connectors (Informacje dotyczące łączników serwera proxy aplikacji usługi Azure AD)](application-proxy-connectors.md). 


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany

Aby upewnić się, że nowy łącznik został zainstalowany poprawnie, możesz użyć witryny Azure Portal lub serwera systemu Windows.

### <a name="verify-the-installation-through-azure-portal"></a>Weryfikacja instalacji za pośrednictwem witryny Azure portal

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Zaloguj się do katalogu dzierżawy w witrynie [Azure Portal](https://portal.azure.com).
2. W panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **serwera Proxy aplikacji** w obszarze **Zarządzaj** sekcji. Na stronie widać wszystkie łączniki i grupy łączników. 
3. Wyświetl łącznika, aby sprawdzić jego szczegóły. Powinno zostać rozszerzone łączniki domyślnie. Jeśli nie jest rozwinięty łącznika którego chcesz wyświetlić, rozwiń łącznika Aby wyświetlić szczegóły. Zielona ikona oznacza, że łącznik jest aktywny i może łączyć się z usługą. Jednak nawet wtedy problem z siecią może uniemożliwiać łącznikowi odbieranie wiadomości. 

    ![Łączników serwera Proxy aplikacji usługi Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Aby uzyskać więcej pomocy przy instalowaniu łącznika, zobacz [Problem podczas instalowania łącznika serwera Proxy aplikacji](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Weryfikacja instalacji za pośrednictwem serwera Windows

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Otwórz Menedżera usług systemu Windows, naciskając klawisz **Windows** i wprowadzając tekst *services.msc*.
2. Sprawdź, czy stan następujących dwóch usług ma wartość **Działa**.
   - **Łącznik serwera Proxy aplikacji usługi AAD Microsoft** zapewnia łączność.
   - **Aktualizator łączników serwera proxy aplikacji usługi Microsoft AAD** to automatyczna usługa aktualizacji. Sprawdza ona dostępność nowych wersji łącznika i aktualizuje go zgodnie z potrzebami.

     ![Usługi łącznika serwera proxy aplikacji — zrzut ekranu](./media/application-proxy-enable/app_proxy_services.png)

3. Jeśli nie ma stanu dla usług **systemem**, kliknij prawym przyciskiem myszy, aby wybrać poszczególnych usług i wybierz **Start**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Dodawanie aplikacji lokalnej do usługi Azure AD

Po przygotowaniu środowiska i zainstalowaniu łącznika możemy dodać aplikacje lokalne do usługi Azure AD.  

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator.
2. W panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**.
3. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **nową aplikację**.
4. Wybierz **aplikacje lokalne**.  
5. W **Dodaj własną aplikację w środowisku lokalnym** sekcji, podaj następujące informacje o aplikacji:

    | Pole | Opis |
    | :---- | :---------- |
    | **Nazwa** | Nazwa aplikacji, która będzie wyświetlana na panelu dostępu oraz w witrynie Azure Portal. |
    | **Wewnętrzny adres URL** | Ten adres URL umożliwia dostęp do aplikacji w sieci prywatnej. Możesz wprowadzić określoną ścieżkę na serwerze zaplecza, która zostanie opublikowana, podczas gdy pozostała część serwera pozostanie nieopublikowana. W ten sposób możesz opublikować wiele lokacji jako różne aplikacje na tym samym serwerze, nadając im różne nazwy i reguły dostępu.<br><br>W przypadku publikowania ścieżki upewnij się, że zawiera ona wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. Na przykład, jeśli aplikacja znajduje się na https:\//yourapp/aplikacja używa obrazów i znajdujący się w https:\//yourapp/nośnika, możesz opublikować https:\//yourapp/ jako ścieżka. Wewnętrzny adres URL nie musi być stroną docelową widoczną dla użytkowników. Aby uzyskać więcej informacji, zobacz [Set a custom home page for published apps (Ustawianie niestandardowej strony głównej dla opublikowanych aplikacji)](application-proxy-configure-custom-home-page.md). |
    | **Zewnętrzny adres URL** | Ten adres umożliwia użytkownikom dostęp do aplikacji spoza sieci. Jeśli nie chcesz używać domyślnej domeny serwera proxy aplikacji, zobacz [Custom domains in Azure AD Application Proxy (Domeny niestandardowe na serwerze proxy aplikacji usługi Azure AD)](application-proxy-configure-custom-domain.md).|
    | **Wstępne uwierzytelnianie** | Sposób, w jaki serwer proxy aplikacji weryfikuje użytkowników przed udzieleniem im dostępu do aplikacji.<br><br>**Azure Active Directory** — serwer proxy aplikacji przekierowuje użytkowników do zalogowania się w usłudze Azure AD, co umożliwia uwierzytelnienie ich uprawnień do katalogu i aplikacji. Zaleca się pozostawienie tej opcji jako domyślny, tak aby można było korzystać z funkcji zabezpieczeń usługi Azure AD, np. dostępu warunkowego i uwierzytelniania wieloskładnikowego. Usługa **Azure Active Directory** jest wymagana do monitorowania aplikacji za pomocą usługi Microsoft Cloud Application Security.<br><br>**Atrybut PASSTHROUGH** — użytkownicy nie mają do uwierzytelniania względem usługi Azure AD, aby uzyskać dostęp do aplikacji. Można jednak na zapleczu skonfigurować wymagania dotyczące uwierzytelniania. |
    | **Grupa łączników** | Łączniki umożliwiają przetwarzanie zdalnego dostępu do aplikacji, a grupy łączników pozwalają klasyfikować łączniki i aplikacje według regionu, sieci lub przeznaczenia. Jeśli nie masz jeszcze żadnych grup łączników, Twoja aplikacja zostanie przypisana do grupy **Domyślne**.<br><br>Jeśli nawiązywanie połączeń w aplikacji odbywa się za pomocą elementów WebSocket, wszystkie łączniki w grupie muszą być w wersji 1.5.612.0 lub nowszej.|

5. W razie potrzeby skonfiguruj **dodatkowe ustawienia**. W przypadku większości aplikacji należy pozostawić domyślne wartości tych ustawień. 

    | Pole | Opis |
    | :---- | :---------- |
    | **Limit czasu aplikacji zaplecza** | Opcję tę ustaw na wartość **Długi** tylko wtedy, gdy uwierzytelnianie aplikacji i łączenie się z nią trwa długo. |
    | **Użyj pliku cookie tylko HTTP** | Ustawienie wartości **Tak** powoduje, że pliki cookie serwera proxy aplikacji będą zawierały flagę HTTPOnly w nagłówku odpowiedzi HTTP. Jeśli korzystasz z usług pulpitu zdalnego, ustaw tę wartość na **Nie**.|
    | **Użyj bezpiecznego pliku cookie**| Ustawienie wartości **Tak** powoduje przesyłanie plików cookie za pośrednictwem bezpiecznego kanału, takiego jak zaszyfrowane żądanie HTTPS.
    | **Używaj trwałego pliku cookie**| Pozostaw wartość **Nie**. Tego ustawienia należy używać tylko dla aplikacji, które nie mogą udostępniać pliki cookie między procesami. Aby uzyskać więcej informacji na temat ustawień plików cookie, zobacz [ustawienia plików Cookie na potrzeby uzyskiwania dostępu do aplikacji lokalnych w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Przekształć adresy URL w nagłówkach** | Pozostaw wartość **Tak**, chyba że aplikacja wymaga, aby żądanie uwierzytelnienia zawierało nagłówek oryginalnego hosta. |
    | **Przekształć adresy URL w treści aplikacji** | Zachowaj tę wartość jako **nie** o ile nie zostały zapisane na stałe HTML łącza do innych aplikacji w środowisku lokalnym i nie używaj domen niestandardowych. Aby uzyskać więcej informacji, zobacz [Link translation with Application Proxy (Przekształcanie linków przy użyciu serwera proxy aplikacji)](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ustaw tę wartość na **Tak**, jeśli zamierzasz monitorować aplikację za pomocą usługi Microsoft Cloud App Security (MCAS). Aby uzyskać więcej informacji, zobacz [Konfigurowanie, monitorowanie dostępu do aplikacji w czasie rzeczywistym za pomocą Microsoft Cloud App Security i Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |
   
6. Wybierz pozycję **Dodaj**.

## <a name="test-the-application"></a>Testowanie aplikacji

Wszystko jest gotowe do sprawdzenia, czy aplikacja została dodana poprawnie. W kolejnych krokach dodasz do aplikacji konto użytkownika i spróbujesz się zalogować.

### <a name="add-a-user-for-testing"></a>Dodawanie użytkownika testowego

Przed dodaniem użytkownika do aplikacji sprawdź, czy konto użytkownika ma już uprawnienia dostępu do aplikacji z sieci firmowej.

Aby dodać użytkownika testowego:

1. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz aplikację, którą chcesz przetestować.
2. Wybierz **wprowadzenie**, a następnie wybierz pozycję **przypisać użytkownika do testowania**.
3. W obszarze **użytkowników i grup**, wybierz opcję **Dodaj użytkownika**.
4. W obszarze **Dodaj przypisanie**, wybierz opcję **użytkowników i grup**. **Użytkowników i grup** pojawi się sekcja. 
5. Wybierz konto, które chcesz dodać. 
6. Wybierz **wybierz**, a następnie wybierz pozycję **przypisać**.

### <a name="test-the-sign-on"></a>Testowanie logowania

Aby przetestować logowanie do aplikacji:

1. W przeglądarce otwórz zewnętrzny adres URL skonfigurowany w kroku publikowania. Powinien zostać wyświetlony ekran startowy.
2. Zaloguj się jako użytkownik, który został utworzony w poprzedniej sekcji.

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [Troubleshoot Application Proxy problems and error messages (Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach)](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przygotowaliśmy środowisko lokalne pod kątem obsługi serwera proxy aplikacji, a następnie zainstalowaliśmy i zarejestrowaliśmy łącznik serwera proxy aplikacji. Później dodaliśmy aplikację do dzierżawy usługi Azure AD. Sprawdziliśmy, czy użytkownik może zalogować się do aplikacji przy użyciu konta usługi Azure AD.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD
> * Sprawdzono, czy użytkownika testowego można logowania do aplikacji przy użyciu konta usługi Azure AD

Możesz przystąpić do konfigurowania aplikacji pod kątem logowania jednokrotnego. Użyj następującego linku, aby wybrać jedną metodę logowania jednokrotnego i samouczki rejestracji jednokrotnej. 

> [!div class="nextstepaction"]
>[Konfigurowanie logowania jednokrotnego](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
