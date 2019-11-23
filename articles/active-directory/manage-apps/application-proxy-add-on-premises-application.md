---
title: Tutorial - Add an on-premises app - Application Proxy in Azure AD
description: Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. This tutorial shows you how to prepare your environment for use with Application Proxy. Then, it uses the Azure portal to add an on-premises application to your Azure AD tenant.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f812bb7d31319476e6b940443e067fac895f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420454"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Add an on-premises application for remote access through Application Proxy in Azure Active Directory

Usługa Azure Active Directory (Azure AD) udostępnia usługę serwera proxy aplikacji, która umożliwia użytkownikom zalogowanym na konto usługi Azure AD dostęp do aplikacji lokalnych. W tym samouczku przygotujemy środowisko do obsługi serwera proxy aplikacji. Gdy środowisko będzie gotowe, za pomocą witryny Azure Portal dodamy aplikację lokalną do dzierżawy usługi Azure AD.

Ten samouczek umożliwia opanowanie następujących czynności:

> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD
> * Verifies a test user can sign on to the application by using an Azure AD account

## <a name="before-you-begin"></a>Przed rozpoczęciem

To add an on-premises application to Azure AD, you need:

* A [Microsoft Azure AD premium subscription](https://azure.microsoft.com/pricing/details/active-directory)
* An application administrator account
* User identities must be synchronized from an on-premises directory or created directly within your Azure AD tenants. Identity synchronization allows Azure AD to pre-authenticate users before granting them access to App Proxy published applications and to have the necessary user identifier information to perform single sign-on (SSO).

### <a name="windows-server"></a>Server systemu Windows

Aby użyć serwera proxy aplikacji, potrzebujesz serwera systemu Windows z systemem Windows Server 2012 R2 lub nowszym. Na serwerze zainstalujesz łącznik serwera proxy aplikacji. Serwer łącznika musi się łączyć z usługami serwera proxy aplikacji na platformie Azure oraz aplikacjami lokalnymi, które zamierzasz opublikować.

Aby zapewnić wysoką dostępność w środowisku produkcyjnym, zalecamy użycie więcej niż jednego serwera systemu Windows. W tym samouczku wystarczy nam jeden serwer systemu Windows.

> [!IMPORTANT]
> If you are installing the connector on Windows Server 2019 there is a HTTP2 limitation. A workaround to use the connector on this version is adding the following registry key and restarting the server. Note, this is a machine registry wide key. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Recommendations for the connector server

1. Serwer łącznika powinien znajdować się blisko serwerów aplikacji, aby wydajność komunikacji między łącznikiem a aplikacją była optymalna. Aby uzyskać więcej informacji, zobacz [Network topology considerations (Kwestie związane z topologią sieci)](application-proxy-network-topology.md).
1. The connector server and the web applications servers should belong to the same Active Directory domain or span trusting domains. Having the servers in the same domain or trusting domains is a requirement for using single sign-on (SSO) with Integrated Windows Authentication (IWA) and Kerberos Constrained Delegation (KCD). Jeśli serwer łącznika znajduje się w innej domenie usługi Active Directory niż serwery aplikacji internetowych, korzystanie z logowania jednokrotnego wymaga użycia delegowania opartego na zasobach. Aby uzyskać więcej informacji, zobacz [KCD for single sign-on with Application Proxy (Ograniczone delegowanie Kerberos dla logowania jednokrotnego przy użyciu serwera proxy aplikacji)](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> If you've deployed Azure AD Password Protection Proxy, do not install Azure AD Application Proxy and Azure AD Password Protection Proxy together on the same machine. Azure AD Application Proxy and Azure AD Password Protection Proxy install different versions of the Azure AD Connect Agent Updater service. These different versions are incompatible when installed together on the same machine.

#### <a name="tls-requirements"></a>TLS requirements

Przed zainstalowaniem łącznika serwera proxy aplikacji na serwerze łącznika systemu Windows należy włączyć protokół TLS 1.2.

Aby włączyć protokół TLS 1.2:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restart the server.

> [!IMPORTANT]
> To provide the best-in-class encryption to our customers, the Application Proxy service limits access to only TLS 1.2 protocols. These changes were gradually rolled out and effective since August 31, 2019. Make sure that all your client-server and browser-server combinations are updated to use TLS 1.2 to maintain connection to Application Proxy service. These include clients your users are using to access applications published through Application Proxy. See Preparing for [TLS 1.2 in Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) for useful references and resources.

## <a name="prepare-your-on-premises-environment"></a>Przygotowywanie środowiska lokalnego

Start by enabling communication to Azure data centers to prepare your environment for Azure AD Application Proxy. If there's a firewall in the path, make sure it's open. An open firewall allows the connector to make HTTPS (TCP) requests to the Application Proxy.

### <a name="open-ports"></a>Otwieranie portów

Otwórz następujące porty dla ruchu **wychodzącego**.

   | Numer portu | Zastosowanie |
   | --- | --- |
   | 80 | Pobieranie list odwołania certyfikatów oraz weryfikowanie certyfikatu SSL |
   | 443 | Cała komunikacja wychodząca do usługi serwera proxy aplikacji |

Jeśli zapora wymusza ruch odpowiednio dla użytkowników, którzy go generują, otwórz również porty 80 i 443 dla ruchu pochodzącego z usług systemu Windows działających jako usługi sieciowe.

### <a name="allow-access-to-urls"></a>Zezwalanie na dostęp do adresów URL

Zezwól na dostęp do następujących adresów URL:

| Adres URL | Zastosowanie |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure uses these URLs to verify certificates. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*.microsoftonline-p.com<br>\*.msauth.net<br>\*.msauthimages.net<br>\*.msecnd.net<br>\*.msftauth.net<br>\*.msftauthimages.net<br>\*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | Łącznik używa tych adresów URL podczas procesu rejestracji. |

You can allow connections to \*.msappproxy.net and \*.servicebus.windows.net if your firewall or proxy lets you configure DNS allow lists. If not, you need to allow access to the [Azure IP ranges and Service Tags - Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Zakresy adresów IP są aktualizowane co tydzień.

## <a name="install-and-register-a-connector"></a>Instalowanie i rejestrowanie łącznika

To use Application Proxy, install a connector on each Windows server you're using with the Application Proxy service. Łącznik to agent, który zarządza połączeniami wychodzącymi z lokalnych serwerów aplikacji do serwera proxy aplikacji w usłudze Azure AD. Łącznik można zainstalować na serwerach, na których zainstalowano również innych agentów uwierzytelniania, takich jak program Azure AD Connect.

Aby zainstalować łącznik:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator aplikacji w katalogu, w którym jest używany serwer proxy aplikacji. Na przykład jeśli domena dzierżawy to contoso.com, administratorem powinien być admin@contoso.com lub dowolny alias administratora w tej domenie.
1. Select your username in the upper-right corner. Verify you're signed in to a directory that uses Application Proxy. If you need to change directories, select **Switch directory** and choose a directory that uses Application Proxy.
1. In left navigation panel, select **Azure Active Directory**.
1. Under **Manage**, select **Application proxy**.
1. Select **Download connector service**.

    ![Download connector service to see the Terms of Service](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Zapoznaj się z warunkami użytkowania usługi. When you're ready, select **Accept terms & Download**.
1. At the bottom of the window, select **Run** to install the connector. Zostanie otwarty kreator instalacji.
1. Follow the instructions in the wizard to install the service. Po wyświetleniu monitu o zarejestrowanie łącznika na serwerze proxy aplikacji w dzierżawie usługi Azure AD, wprowadź swoje poświadczenia administratora aplikacji.
    - Jeśli korzystasz z programu Internet Explorer (IE) i opcja **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** ma wartość **Wł**, ekran rejestracji może nie być widoczny. Aby uzyskać dostęp, wykonaj instrukcje podane w komunikacie o błędzie. Make sure that **Internet Explorer Enhanced Security Configuration** is set to **Off**.

### <a name="general-remarks"></a>Uwagi ogólne

Jeśli wcześniej zainstalowano łącznik, zainstaluj go ponownie, aby uzyskać najnowszą wersję. To see information about previously released versions and what changes they include, see [Application Proxy: Version Release History](application-proxy-release-version-history.md).

Jeśli masz więcej niż jeden serwer systemu Windows dla aplikacji lokalnych, musisz zainstalować i zarejestrować łącznik na każdym serwerze. Możesz tworzyć grupy łączników. Aby uzyskać więcej informacji, zobacz [Grupy łączników](application-proxy-connector-groups.md).

Jeśli do łączenia się z Internetem w Twojej organizacji są używane serwery proxy, musisz je skonfigurować pod kątem serwera proxy aplikacji.  Aby uzyskać więcej informacji, zobacz [Work with existing on-premises proxy servers (Praca z istniejącymi lokalnymi serwerami proxy)](application-proxy-configure-connectors-with-proxy-servers.md). 

Aby uzyskać informacje o łącznikach oraz ich aktualizowaniu, a także wskazówki na temat planowania wydajności, zobacz [Understand Azure AD Application Proxy connectors (Informacje dotyczące łączników serwera proxy aplikacji usługi Azure AD)](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany

Aby upewnić się, że nowy łącznik został zainstalowany poprawnie, możesz użyć witryny Azure Portal lub serwera systemu Windows.

### <a name="verify-the-installation-through-azure-portal"></a>Verify the installation through Azure portal

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Zaloguj się do katalogu dzierżawy w witrynie [Azure Portal](https://portal.azure.com).
1. In the left navigation panel, select **Azure Active Directory**, and then select **Application Proxy** under the **Manage** section. Na stronie widać wszystkie łączniki i grupy łączników.
1. View a connector to verify its details. The connectors should be expanded by default. If the connector you want to view isn't expanded, expand the connector to view the details. Zielona ikona oznacza, że łącznik jest aktywny i może łączyć się z usługą. Jednak nawet wtedy problem z siecią może uniemożliwiać łącznikowi odbieranie wiadomości.

    ![Azure AD Application Proxy Connectors](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

For more help with installing a connector, see [Problem installing the Application Proxy Connector](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verify the installation through your Windows server

Aby sprawdzić, czy łącznik został poprawnie zainstalowany i zarejestrowany:

1. Otwórz Menedżera usług systemu Windows, naciskając klawisz **Windows** i wprowadzając tekst *services.msc*.
1. Sprawdź, czy stan następujących dwóch usług ma wartość **Działa**.
   - **Microsoft AAD Application Proxy Connector** enables connectivity.
   - **Aktualizator łączników serwera proxy aplikacji usługi Microsoft AAD** to automatyczna usługa aktualizacji. Sprawdza ona dostępność nowych wersji łącznika i aktualizuje go zgodnie z potrzebami.

     ![Usługi łącznika serwera proxy aplikacji — zrzut ekranu](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. If the status for the services isn't **Running**, right-click to select each service and choose **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Dodawanie aplikacji lokalnej do usługi Azure AD

Po przygotowaniu środowiska i zainstalowaniu łącznika możemy dodać aplikacje lokalne do usługi Azure AD.  

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator.
2. In the left navigation panel, select **Azure Active Directory**.
3. Select **Enterprise applications**, and then select **New application**.
4. In the **On-premises applications** section, select **Add an on-premises application**.
5. In the **Add your own on-premises application** section, provide the following information about your application:

    | Pole | Opis |
    | :---- | :---------- |
    | **Nazwa** | Nazwa aplikacji, która będzie wyświetlana na panelu dostępu oraz w witrynie Azure Portal. |
    | **Wewnętrzny adres URL** | Ten adres URL umożliwia dostęp do aplikacji w sieci prywatnej. Możesz wprowadzić określoną ścieżkę na serwerze zaplecza, która zostanie opublikowana, podczas gdy pozostała część serwera pozostanie nieopublikowana. W ten sposób możesz opublikować wiele lokacji jako różne aplikacje na tym samym serwerze, nadając im różne nazwy i reguły dostępu.<br><br>W przypadku publikowania ścieżki upewnij się, że zawiera ona wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. For example, if your app is at https:\//yourapp/app and uses images located at https:\//yourapp/media, then you should publish https:\//yourapp/ as the path. Wewnętrzny adres URL nie musi być stroną docelową widoczną dla użytkowników. Aby uzyskać więcej informacji, zobacz [Set a custom home page for published apps (Ustawianie niestandardowej strony głównej dla opublikowanych aplikacji)](application-proxy-configure-custom-home-page.md). |
    | **Zewnętrzny adres URL** | Ten adres umożliwia użytkownikom dostęp do aplikacji spoza sieci. Jeśli nie chcesz używać domyślnej domeny serwera proxy aplikacji, zobacz [Custom domains in Azure AD Application Proxy (Domeny niestandardowe na serwerze proxy aplikacji usługi Azure AD)](application-proxy-configure-custom-domain.md).|
    | **Wstępne uwierzytelnianie** | Sposób, w jaki serwer proxy aplikacji weryfikuje użytkowników przed udzieleniem im dostępu do aplikacji.<br><br>**Azure Active Directory** — serwer proxy aplikacji przekierowuje użytkowników do zalogowania się w usłudze Azure AD, co umożliwia uwierzytelnienie ich uprawnień do katalogu i aplikacji. We recommend keeping this option as the default so that you can take advantage of Azure AD security features like Conditional Access and Multi-Factor Authentication. Usługa **Azure Active Directory** jest wymagana do monitorowania aplikacji za pomocą usługi Microsoft Cloud Application Security.<br><br>**Passthrough** - Users don't have to authenticate against Azure AD to access the application. Można jednak na zapleczu skonfigurować wymagania dotyczące uwierzytelniania. |
    | **Grupa łączników** | Łączniki umożliwiają przetwarzanie zdalnego dostępu do aplikacji, a grupy łączników pozwalają klasyfikować łączniki i aplikacje według regionu, sieci lub przeznaczenia. Jeśli nie masz jeszcze żadnych grup łączników, Twoja aplikacja zostanie przypisana do grupy **Domyślne**.<br><br>Jeśli nawiązywanie połączeń w aplikacji odbywa się za pomocą elementów WebSocket, wszystkie łączniki w grupie muszą być w wersji 1.5.612.0 lub nowszej.|

6. W razie potrzeby skonfiguruj **dodatkowe ustawienia**. W przypadku większości aplikacji należy pozostawić domyślne wartości tych ustawień. 

    | Pole | Opis |
    | :---- | :---------- |
    | **Limit czasu aplikacji zaplecza** | Opcję tę ustaw na wartość **Długi** tylko wtedy, gdy uwierzytelnianie aplikacji i łączenie się z nią trwa długo. At default, the backend application timeout has a length of 85 seconds. When set to long, the backend timeout is increased to 180 seconds. |
    | **Użyj pliku cookie tylko HTTP** | Ustawienie wartości **Tak** powoduje, że pliki cookie serwera proxy aplikacji będą zawierały flagę HTTPOnly w nagłówku odpowiedzi HTTP. Jeśli korzystasz z usług pulpitu zdalnego, ustaw tę wartość na **Nie**.|
    | **Użyj bezpiecznego pliku cookie**| Ustawienie wartości **Tak** powoduje przesyłanie plików cookie za pośrednictwem bezpiecznego kanału, takiego jak zaszyfrowane żądanie HTTPS.
    | **Używaj trwałego pliku cookie**| Pozostaw wartość **Nie**. Only use this setting for applications that can't share cookies between processes. For more information about cookie settings, see [Cookie settings for accessing on-premises applications in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **Przekształć adresy URL w nagłówkach** | Pozostaw wartość **Tak**, chyba że aplikacja wymaga, aby żądanie uwierzytelnienia zawierało nagłówek oryginalnego hosta. |
    | **Przekształć adresy URL w treści aplikacji** | Keep this value as **No** unless you have hardcoded HTML links to other on-premises applications and don't use custom domains. Aby uzyskać więcej informacji, zobacz [Link translation with Application Proxy (Przekształcanie linków przy użyciu serwera proxy aplikacji)](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ustaw tę wartość na **Tak**, jeśli zamierzasz monitorować aplikację za pomocą usługi Microsoft Cloud App Security (MCAS). For more information, see [Configure real-time application access monitoring with Microsoft Cloud App Security and Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Wybierz pozycję **Dodaj**.

## <a name="test-the-application"></a>Testowanie aplikacji

Wszystko jest gotowe do sprawdzenia, czy aplikacja została dodana poprawnie. W kolejnych krokach dodasz do aplikacji konto użytkownika i spróbujesz się zalogować.

### <a name="add-a-user-for-testing"></a>Dodawanie użytkownika testowego

Przed dodaniem użytkownika do aplikacji sprawdź, czy konto użytkownika ma już uprawnienia dostępu do aplikacji z sieci firmowej.

Aby dodać użytkownika testowego:

1. Select **Enterprise applications**, and then select the application you want to test.
2. Select **Getting started**, and then select **Assign a user for testing**.
3. Under **Users and groups**, select **Add user**.
4. Under **Add assignment**, select **Users and groups**. The **User and groups** section appears.
5. Choose the account you want to add.
6. Choose **Select**, and then select **Assign**.

### <a name="test-the-sign-on"></a>Testowanie logowania

To test the sign-on to the application:

1. W przeglądarce otwórz zewnętrzny adres URL skonfigurowany w kroku publikowania. Powinien zostać wyświetlony ekran startowy.
1. Sign in as the user you created in the previous section.

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [Troubleshoot Application Proxy problems and error messages (Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach)](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przygotowaliśmy środowisko lokalne pod kątem obsługi serwera proxy aplikacji, a następnie zainstalowaliśmy i zarejestrowaliśmy łącznik serwera proxy aplikacji. Później dodaliśmy aplikację do dzierżawy usługi Azure AD. Sprawdziliśmy, czy użytkownik może zalogować się do aplikacji przy użyciu konta usługi Azure AD.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
> * Otwieranie portów dla ruchu wychodzącego i zezwalanie na dostęp do określonych adresów URL
> * Instalowanie łącznika na serwerze systemu Windows i rejestrowanie łącznika na serwerze proxy aplikacji
> * Sprawdzanie, czy łącznik został poprawnie zainstalowany i zarejestrowany
> * Dodawanie aplikacji lokalnej do dzierżawy usługi Azure AD
> * Verified a test user can sign on to the application by using an Azure AD account

Możesz przystąpić do konfigurowania aplikacji pod kątem logowania jednokrotnego. Use the following link to choose a single sign-on method and to find single sign-on tutorials.

> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
