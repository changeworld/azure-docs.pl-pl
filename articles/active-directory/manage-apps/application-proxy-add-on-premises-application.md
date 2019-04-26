---
title: Dodawanie aplikacji lokalnej — serwer proxy aplikacji w usłudze Azure Active Directory | Microsoft Docs
description: Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. W tym samouczku pokazano, jak przygotować środowisko do obsługi serwera proxy aplikacji, a następnie dodać aplikację lokalną do dzierżawy usługi Azure AD za pomocą witryny Azure Portal.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc454fdba6ec875c3d3b572a7aba91bb9d389845
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294808"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Samouczek: Dodawanie aplikacji lokalnej na potrzeby dostępu zdalnego za pomocą serwera proxy aplikacji w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. W tym samouczku przygotujemy środowisko do obsługi serwera proxy aplikacji. Gdy środowisko będzie gotowe, za pomocą witryny Azure Portal dodamy aplikację lokalną do dzierżawy usługi Azure AD.

Ten samouczek umożliwia opanowanie następujących czynności:

> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL.
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji.
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany.
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD.
> * Sprawdzanie, czy użytkownik testowy może zalogować się do aplikacji przy użyciu konta usługi Azure AD.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby dodać aplikację do swojej dzierżawy, potrzebujesz:

* [subskrypcji usługi Microsoft Azure AD w warstwie Podstawowa lub Premium](https://azure.microsoft.com/pricing/details/active-directory), 
* konta administratora aplikacji.

### <a name="windows-server"></a>Server systemu Windows

Aby użyć serwera proxy aplikacji, potrzebujesz serwera systemu Windows z systemem Windows Server 2012 R2 lub nowszym. Na serwerze zainstalujesz łącznik serwera proxy aplikacji. Serwer łącznika musi się łączyć z usługami serwera proxy aplikacji na platformie Azure oraz aplikacjami lokalnymi, które zamierzasz opublikować.

Aby zapewnić wysoką dostępność w środowisku produkcyjnym, zalecamy użycie więcej niż jednego serwera systemu Windows. W tym samouczku wystarczy nam jeden serwer systemu Windows.

#### <a name="recommendations-for-the-connector-server"></a>Zalecenia dotyczące serwera łącznika

1. Serwer łącznika powinien znajdować się blisko serwerów aplikacji, aby wydajność komunikacji między łącznikiem a aplikacją była optymalna. Aby uzyskać więcej informacji, zobacz [Network topology considerations (Kwestie związane z topologią sieci)](application-proxy-network-topology.md).

2. Serwer łącznika i serwerów aplikacji sieci web powinna należeć do tej samej domeny usługi Active Directory lub span domenach ufających. Posiadających serwery w tej samej domenie lub domenach ufających jest wymaganie dotyczące korzystania z logowania jednokrotnego (SSO) przy użyciu zintegrowanego Windows Authentication (Zintegrowane) i delegowanie ograniczone protokołu Kerberos (KCD). Jeśli serwer łącznika znajduje się w innej domenie usługi Active Directory niż serwery aplikacji internetowych, korzystanie z logowania jednokrotnego wymaga użycia delegowania opartego na zasobach. Aby uzyskać więcej informacji, zobacz [KCD for single sign-on with Application Proxy (Ograniczone delegowanie Kerberos dla logowania jednokrotnego przy użyciu serwera proxy aplikacji)](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

Przed zainstalowaniem łącznika serwera proxy aplikacji na serwerze łącznika systemu Windows należy włączyć protokół TLS 1.2. Istniejące łączniki w wersjach starszych niż 1.5.612.0 aktualnie obsługują wcześniejsze wersje protokołu TLS. 

Aby włączyć protokół TLS 1.2:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Uruchom ponownie serwer.

## <a name="prepare-your-on-premises-environment"></a>Przygotowywanie środowiska lokalnego

Aby przygotować środowisko do obsługi serwera proxy aplikacji usługi Azure AD, najpierw musisz włączyć komunikację z centrami danych platformy Azure. Jeśli na ścieżce znajduje się zapora, upewnij się, że jest otwarta, aby umożliwić łącznikowi wysyłanie żądań protokołu HTTPS (TCP) do serwera proxy aplikacji.

### <a name="open-ports"></a>Otwieranie portów

Otwórz następujące porty dla ruchu **wychodzącego**. 

   | Numer portu | Zastosowanie |
   | --- | --- |
   | 80 | Pobieranie list odwołania certyfikatów oraz weryfikowanie certyfikatu SSL |
   | 443 | Cała komunikacja wychodząca do usługi serwera proxy aplikacji |

Jeśli zapora wymusza ruch odpowiednio dla użytkowników, którzy go generują, otwórz również porty 80 i 443 dla ruchu pochodzącego z usług systemu Windows działających jako usługi sieciowe.

Jeśli już używasz serwera proxy aplikacji, być może masz zainstalowaną starszą wersję łącznika.  Aby zainstalować najnowszą wersję łącznika, wykonaj czynności opisane w tym samouczku. Wersje starsze niż 1.5.132.0 wymagają również otwarcia następujących portów: 5671, 8080, 9090–9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL

Zezwól na dostęp do następujących adresów URL:

| Adres URL | Zastosowanie |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Platforma Azure używa tych adresów URL do weryfikacji certyfikatów |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Łącznik używa tych adresów URL podczas procesu rejestracji. |

Jeśli zapora lub serwer proxy umożliwia umieszczanie nazw DNS na białej liście, możesz dodać do niej połączenia z adresami URL \*msappproxy.net i \*servicebus.windows.net. W przeciwnym razie musisz zezwolić na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Zakresy adresów IP są aktualizowane co tydzień.

## <a name="install-and-register-a-connector"></a>Instalowanie i rejestrowanie łącznika

Aby korzystać z serwera proxy aplikacji, musisz zainstalować łącznik na każdym serwerze systemu Windows, na którym chcesz używać usługi serwera proxy aplikacji. Łącznik to agent, który zarządza połączeniami wychodzącymi z lokalnych serwerów aplikacji do serwera proxy aplikacji w usłudze Azure AD. Łącznik można zainstalować na serwerach, na których zainstalowano również innych agentów uwierzytelniania, takich jak program Azure AD Connect.

Aby zainstalować łącznik:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator aplikacji w katalogu, w którym jest używany serwer proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias administratora w tej domenie.
2. Bieżący katalog jest widoczny pod nazwą użytkownika w prawym górnym rogu. Sprawdź, czy zalogowano się do katalogu, w którym jest używany serwer proxy aplikacji. Jeśli trzeba zmienić katalogi, wybierz tę ikonę.
3. W bloku po lewej stronie kliknij pozycję **Azure Active Directory**, a następnie **Serwer proxy aplikacji**.
4. Kliknij pozycję **Pobierz usługę łącznika**.
5. Zapoznaj się z warunkami użytkowania usługi.  Gdy wszystko będzie gotowe, kliknij przycisk **Zaakceptuj warunki i pobierz**.
6. W dolnej części okna pojawi się monit o pobranie programu **AADApplicationProxyConnectorInstaller.exe**. Kliknij przycisk **Uruchom**, aby zainstalować łącznik. Zostanie otwarty kreator instalacji. 
7. Postępuj zgodnie z instrukcjami w kreatorze, aby przeprowadzić instalację. Po wyświetleniu monitu o zarejestrowanie łącznika na serwerze proxy aplikacji w dzierżawie usługi Azure AD, wprowadź swoje poświadczenia administratora aplikacji.
    - Jeśli korzystasz z programu Internet Explorer (IE) i opcja **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** ma wartość **Wł**, ekran rejestracji może nie być widoczny. Aby uzyskać dostęp, wykonaj instrukcje podane w komunikacie o błędzie. Upewnij się, że opcja Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer ma wartość **Wył**.

### <a name="general-remarks"></a>Uwagi ogólne

Jeśli wcześniej zainstalowano łącznik, zainstaluj go ponownie, aby uzyskać najnowszą wersję. Aby wyświetlić informacje o wersjach wcześniej wydanych i jakich zmian, ich zawierają, zobacz [Application Proxy — Historia wersji](application-proxy-release-version-history.md).

Jeśli masz więcej niż jeden serwer systemu Windows dla aplikacji lokalnych, musisz zainstalować i zarejestrować łącznik na każdym serwerze. Możesz tworzyć grupy łączników. Aby uzyskać więcej informacji, zobacz [Grupy łączników](application-proxy-connector-groups.md). 

Jeśli do łączenia się z Internetem w Twojej organizacji są używane serwery proxy, musisz je skonfigurować pod kątem serwera proxy aplikacji.  Aby uzyskać więcej informacji, zobacz [Work with existing on-premises proxy servers (Praca z istniejącymi lokalnymi serwerami proxy)](application-proxy-configure-connectors-with-proxy-servers.md). 

Aby uzyskać informacje o łącznikach oraz ich aktualizowaniu, a także wskazówki na temat planowania wydajności, zobacz [Understand Azure AD Application Proxy connectors (Informacje dotyczące łączników serwera proxy aplikacji usługi Azure AD)](application-proxy-connectors.md). 


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany

Aby upewnić się, że nowy łącznik został zainstalowany poprawnie, możesz użyć witryny Azure Portal lub serwera systemu Windows.

### <a name="verify---azure-portal"></a>Weryfikowanie — witryna Azure Portal

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Zaloguj się do katalogu dzierżawy w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Azure Active Directory**, a następnie **Serwer proxy aplikacji**. Na stronie widać wszystkie łączniki i grupy łączników. 
3. Wybierz łącznik, aby sprawdzić jego szczegóły. Zielona ikona oznacza, że łącznik jest aktywny i może łączyć się z usługą. Jednak nawet wtedy problem z siecią może uniemożliwiać łącznikowi odbieranie wiadomości. 

    ![Łączniki serwera proxy aplikacji usługi Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Aby uzyskać dodatkową pomoc przy instalowaniu łącznika, zobacz [Problems installing an Application Proxy Connector (Problemy z instalacją łącznika serwera proxy aplikacji)](application-proxy-connector-installation-problem.md).

### <a name="verify---windows-server"></a>Weryfikowanie — serwer systemu Windows

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Otwórz Menedżera usług systemu Windows, naciskając klawisz **Windows** i wprowadzając tekst *services.msc*.
2. Sprawdź, czy stan następujących dwóch usług ma wartość **Działa**.
   - **Łącznik serwera proxy aplikacji usługi Microsoft AAD** umożliwia łączność
   - **Aktualizator łączników serwera proxy aplikacji usługi Microsoft AAD** to automatyczna usługa aktualizacji. Sprawdza ona dostępność nowych wersji łącznika i aktualizuje go zgodnie z potrzebami.

     ![Usługi łącznika serwera proxy aplikacji — zrzut ekranu](./media/application-proxy-enable/app_proxy_services.png)

3. Jeśli stan usługi nie ma wartości **Działa**, kliknij prawym przyciskiem myszy każdą usługę i wybierz polecenie **Uruchom**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Dodawanie aplikacji lokalnej do usługi Azure AD

Po przygotowaniu środowiska i zainstalowaniu łącznika możemy dodać aplikacje lokalne do usługi Azure AD.  

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator.
2. Wybierz pozycję **Azure Active Directory** > **Aplikacje dla przedsiębiorstw** > **Nowa aplikacja**.

    ![Dodawanie aplikacji dla przedsiębiorstw](./media/application-proxy-publish-azure-portal/add-app.png)

3. Wybierz pozycję **Wszystkie**, a następnie **Aplikacja lokalna**.  

    ![Dodawanie własnej aplikacji](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. W bloku **Dodaj własną aplikację lokalną** podaj następujące informacje dotyczące aplikacji:

    ![Konfigurowanie aplikacji w środowisku lokalnym](./media/application-proxy-add-on-premises-application/add-on-premises-app-with-application-proxy-updated.png)

    | Pole | Opis |
    | :---- | :---------- |
    | **Nazwa** | Nazwa aplikacji, która będzie wyświetlana na panelu dostępu oraz w witrynie Azure Portal. |
    | **Wewnętrzny adres URL** | Ten adres URL umożliwia dostęp do aplikacji w sieci prywatnej. Możesz wprowadzić określoną ścieżkę na serwerze zaplecza, która zostanie opublikowana, podczas gdy pozostała część serwera pozostanie nieopublikowana. W ten sposób możesz opublikować wiele lokacji jako różne aplikacje na tym samym serwerze, nadając im różne nazwy i reguły dostępu.<br><br>W przypadku publikowania ścieżki upewnij się, że zawiera ona wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. Na przykład, jeśli aplikacja znajduje się na https:\//yourapp/aplikacja używa obrazów i znajdujący się w https:\//yourapp/nośnika, możesz opublikować https:\//yourapp/ jako ścieżka. Wewnętrzny adres URL nie musi być stroną docelową widoczną dla użytkowników. Aby uzyskać więcej informacji, zobacz [Set a custom home page for published apps (Ustawianie niestandardowej strony głównej dla opublikowanych aplikacji)](application-proxy-configure-custom-home-page.md). |
    | **Zewnętrzny adres URL** | Ten adres umożliwia użytkownikom dostęp do aplikacji spoza sieci. Jeśli nie chcesz używać domyślnej domeny serwera proxy aplikacji, zobacz [Custom domains in Azure AD Application Proxy (Domeny niestandardowe na serwerze proxy aplikacji usługi Azure AD)](application-proxy-configure-custom-domain.md).|
    | **Wstępne uwierzytelnianie** | Sposób, w jaki serwer proxy aplikacji weryfikuje użytkowników przed udzieleniem im dostępu do aplikacji.<br><br>**Azure Active Directory** — serwer proxy aplikacji przekierowuje użytkowników do zalogowania się w usłudze Azure AD, co umożliwia uwierzytelnienie ich uprawnień do katalogu i aplikacji. Zalecamy pozostawienie domyślnego ustawienia tej opcji, ponieważ umożliwia ono korzystanie z funkcji zabezpieczeń usługi Azure AD, np. dostępu warunkowego i uwierzytelniania wieloskładnikowego. Usługa **Azure Active Directory** jest wymagana do monitorowania aplikacji za pomocą usługi Microsoft Cloud Application Security.<br><br>**Przekazywanie** — użytkownicy nie muszą uwierzytelniać się w usłudze Azure Active Directory, aby uzyskać dostęp do aplikacji. Można jednak na zapleczu skonfigurować wymagania dotyczące uwierzytelniania. |
    | **Grupa łączników** | Łączniki umożliwiają przetwarzanie zdalnego dostępu do aplikacji, a grupy łączników pozwalają klasyfikować łączniki i aplikacje według regionu, sieci lub przeznaczenia. Jeśli nie masz jeszcze żadnych grup łączników, Twoja aplikacja zostanie przypisana do grupy **Domyślne**.<br><br>Jeśli nawiązywanie połączeń w aplikacji odbywa się za pomocą elementów WebSocket, wszystkie łączniki w grupie muszą być w wersji 1.5.612.0 lub nowszej.|

5. W razie potrzeby skonfiguruj **dodatkowe ustawienia**. W przypadku większości aplikacji należy pozostawić domyślne wartości tych ustawień. 

    | Pole | Opis |
    | :---- | :---------- |
    | **Limit czasu aplikacji zaplecza** | Opcję tę ustaw na wartość **Długi** tylko wtedy, gdy uwierzytelnianie aplikacji i łączenie się z nią trwa długo. |
    | **Użyj pliku cookie tylko HTTP** | Ustawienie wartości **Tak** powoduje, że pliki cookie serwera proxy aplikacji będą zawierały flagę HTTPOnly w nagłówku odpowiedzi HTTP. Jeśli korzystasz z usług pulpitu zdalnego, ustaw tę wartość na **Nie**.|
    | **Użyj bezpiecznego pliku cookie**| Ustawienie wartości **Tak** powoduje przesyłanie plików cookie za pośrednictwem bezpiecznego kanału, takiego jak zaszyfrowane żądanie HTTPS.
    | **Używaj trwałego pliku cookie**| Pozostaw wartość **Nie**. Tego ustawienia należy używać tylko w przypadku aplikacji, które nie mogą udostępniać plików cookie między procesami. Aby uzyskać więcej informacji na temat ustawień plików cookie, zobacz [Ustawienia plików cookie na potrzeby uzyskiwania dostępu do aplikacji lokalnych w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)
    | **Przekształć adresy URL w nagłówkach** | Pozostaw wartość **Tak**, chyba że aplikacja wymaga, aby żądanie uwierzytelnienia zawierało nagłówek oryginalnego hosta. |
    | **Przekształć adresy URL w treści aplikacji** | Pozostaw wartość **Nie**, chyba że używasz zapisanych na stałe linków HTML do innych aplikacji lokalnych i nie korzystasz z domen niestandardowych. Aby uzyskać więcej informacji, zobacz [Link translation with Application Proxy (Przekształcanie linków przy użyciu serwera proxy aplikacji)](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ustaw tę wartość na **Tak**, jeśli zamierzasz monitorować aplikację za pomocą usługi Microsoft Cloud App Security (MCAS). Aby uzyskać więcej informacji, zobacz [Configure real-time application access monitoring with Microsoft Cloud App Security and Azure Active Directory (Konfigurowanie monitorowania dostępu do aplikacji w czasie rzeczywistym za pomocą usług Microsoft Cloud App Security i Azure Active Directory)](application-proxy-integrate-with-microsoft-cloud-application-security.md) |
   
6. Wybierz pozycję **Dodaj**.

## <a name="test-the-application"></a>Testowanie aplikacji

Wszystko jest gotowe do sprawdzenia, czy aplikacja została dodana poprawnie. W kolejnych krokach dodasz do aplikacji konto użytkownika i spróbujesz się zalogować.

### <a name="add-a-user-for-testing"></a>Dodawanie użytkownika testowego

Przed dodaniem użytkownika do aplikacji sprawdź, czy konto użytkownika ma już uprawnienia dostępu do aplikacji z sieci firmowej.

Aby dodać użytkownika testowego:

1. W bloku **Szybki start** wybierz pozycję **Przypisywanie użytkownika na potrzeby testowania**.

    ![Przypisywanie użytkownika na potrzeby testowania](./media/application-proxy-publish-azure-portal/assign-user.png)

2. W bloku **Użytkownicy i grupy** wybierz pozycję **Dodaj użytkownika**.

    ![Dodawanie użytkownika lub grupy](./media/application-proxy-publish-azure-portal/add-user.png)

3. W bloku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**, a następnie wybierz konto, które chcesz dodać. 
4. Wybierz opcję **Przypisz**.

### <a name="test-the-sign-on"></a>Testowanie logowania

Aby przetestować logowanie do aplikacji:

1. W przeglądarce otwórz zewnętrzny adres URL skonfigurowany w kroku publikowania. 
2. Powinien zostać wyświetlony ekran startowy.
3. Spróbuj zalogować się jako użytkownik, który został utworzony w poprzedniej sekcji.

    ![Testowanie opublikowanej aplikacji](./media/application-proxy-publish-azure-portal/test-app.png)

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [Troubleshoot Application Proxy problems and error messages (Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach)](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przygotowaliśmy środowisko lokalne pod kątem obsługi serwera proxy aplikacji, a następnie zainstalowaliśmy i zarejestrowaliśmy łącznik serwera proxy aplikacji. Później dodaliśmy aplikację do dzierżawy usługi Azure AD. Sprawdziliśmy, czy użytkownik może zalogować się do aplikacji przy użyciu konta usługi Azure AD.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD
> * Sprawdzanie, czy użytkownik testowy może zalogować się do aplikacji przy użyciu konta usługi Azure AD.

Możesz przystąpić do konfigurowania aplikacji pod kątem logowania jednokrotnego. Skorzystaj z poniższego linku, aby wybrać metodę logowania jednokrotnego oraz znaleźć samouczki dotyczące logowania jednokrotnego. 

> [!div class="nextstepaction"]
>[Konfigurowanie logowania jednokrotnego](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
