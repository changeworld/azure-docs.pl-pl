---
title: Automatyzowanie aprowizacji w aplikacjach przy użyciu Standard SCIM w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory automatycznie aprowizować użytkowników i grup do aplikacji lub tożsamość magazynu, który fronted przez usługę sieci web, za pomocą interfejsu zdefiniowane w specyfikacji protokołu Standard SCIM
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 101b6531601458c33b8cede72f3a63f586df08bb
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224830"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Przy użyciu systemu dla Standard międzydomenowe zarządzania tożsamościami (SCIM), aby automatycznie aprowizować użytkowników i grup z usługi Azure Active Directory do aplikacji

## <a name="overview"></a>Przegląd
Azure Active Directory (Azure AD), mogą automatycznie obsługiwać użytkowników i grup do aplikacji lub tożsamość magazynu, który jest fronted przez usługę sieci web za pomocą interfejsu określonego w [System protokołu Standard międzydomenowe Identity Management (SCIM) w wersji 2.0 Specyfikacja](https://tools.ietf.org/html/draft-ietf-scim-api-19). Usługa Azure Active Directory mogą wysyłać żądania do tworzenia, modyfikowania lub usuwania przypisanych użytkowników i grupy z usługą sieci web. Usługa sieci web może dokonywać translacji te żądania do operacji na docelowym magazynu tożsamości. 

![][0]
*Rysunek 1: Inicjowanie obsługi administracyjnej z usługi Azure Active Directory, do magazynu tożsamości za pomocą usługi sieci web*

Ta funkcja może służyć w połączeniu z funkcją "Przynieś własną aplikację" w usłudze Azure AD. Ta funkcja umożliwia logowanie jednokrotne i automatyczne aprowizowanie dla aplikacji, które są fronted przez usługę sieci web, Standard SCIM użytkowników.

Istnieją dwa przypadki użycia dla usługi Azure Active Directory przy użyciu Standard SCIM:

* **Inicjowanie obsługi administracyjnej użytkowników i grup do aplikacji obsługujących standard SCIM** — aplikacje, które obsługują standard SCIM w wersji 2.0 i używać tokenów elementu nośnego OAuth dla uwierzytelniania współpracuje z usługą Azure AD bez konfiguracji.
  
* **Kompilowanie rozwiązania inicjowania obsługi administracyjnej dla aplikacji, która obsługę innych oparte na interfejsie API** — w przypadku aplikacji innych niż standard SCIM można utworzyć punktu końcowego Standard SCIM tłumaczenie z punktu końcowego usługi Azure AD w Standard SCIM i dowolny interfejs API obsługuje aplikację Inicjowanie obsługi użytkowników. Aby ułatwić tworzenie punktu końcowego Standard SCIM istnieje biblioteki Common Language Infrastructure (CLI) oraz przykłady kodu, które pokazują, jak Podaj punkt końcowy Standard SCIM i tłumaczenie wiadomości Standard SCIM.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Inicjowanie obsługi administracyjnej użytkowników i grup do aplikacji obsługujących standard SCIM
Usługa Azure AD można skonfigurować, aby automatycznie aprowizować przypisanych użytkowników i grup do aplikacji, które implementują [System do zarządzania tożsamościami międzydomenowe 2 (Standard SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) usługi sieci web, a następnie zaakceptuj tokenów elementu nośnego OAuth do uwierzytelniania. W ramach specyfikacji Standard SCIM w wersji 2.0 aplikacje muszą spełniać następujące wymagania:

* Obsługuje tworzenie użytkowników i/lub grupy, zgodnie z sekcji 3.3 protokołu Standard SCIM.  
* Obsługa modyfikowania użytkowników i/lub grup w żądaniach patch zgodnie z sekcji 3.5.2 protokołu Standard SCIM.  
* Obsługuje pobieranie znanych zasobów zgodnie z sekcji 3.4.1 protokołu Standard SCIM.  
* Obsługuje wykonywanie kwerend użytkowników i/lub grupy, zgodnie z sekcji 3.4.2 protokołu Standard SCIM.  Domyślnie użytkownicy będą pytani przez externalId i grup są badane przez Nazwa wyświetlana.  
* Obsługa zapytań użytkownika według Identyfikatora i przez Menedżera zgodnie z sekcji 3.4.2 protokołu Standard SCIM.  
* Obsługuje wykonywanie zapytań dotyczących grup według Identyfikatora, a także przez członka zgodnie z sekcji 3.4.2 protokołu Standard SCIM.  
* Akceptuje tokenów elementu nośnego OAuth dla autoryzacji zgodnie z sekcji 2.1 protokołu Standard SCIM.

Skontaktuj się z dostawcą aplikacji lub dokumentacji dostawcy aplikacji dla instrukcji zgodność z tymi wymaganiami.

### <a name="getting-started"></a>Wprowadzenie
Aplikacje, które obsługują profile Standard SCIM opisane w tym artykule może być połączona z usługą Azure Active Directory przy użyciu funkcji "aplikacji spoza galerii" w galerii aplikacji usługi Azure AD. Po nawiązaniu połączenia usługi Azure AD działa proces synchronizacji co 40 minut, gdzie go zapytania aplikacji Standard SCIM z punktu końcowego dla przypisanych użytkowników i grup i tworzy lub modyfikuje je zgodnie ze szczegółami przypisania.

**Łączenie aplikacji, która obsługuje standard SCIM:**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com). 
2. Przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**i wybierz **nowej aplikacji > wszystkie > Aplikacja spoza galerii**.
3. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Dodaj** ikonę, aby utworzyć obiekt aplikacji.
    
  ![][1]
  *Rysunek 2: Galeria aplikacji usługi Azure AD*
    
4. Z wyświetlonego ekranu wybierz **aprowizacji** karty w lewej kolumnie.
5. W **inicjowania obsługi trybu** menu, wybierz opcję **automatyczne**.
    
  ![][2]
  *Rysunek 3: Konfigurowanie aprowizacji w witrynie Azure portal*
    
6. W **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard SCIM aplikacji. Przykład: https://api.contoso.com/scim/v2/
7. Jeśli punkt końcowy Standard SCIM wymaga tokenu elementu nośnego OAuth od wystawcy innych niż Usługa Azure AD, następnie skopiuj wymagany token elementu nośnego OAuth do opcjonalnego **klucz tajny tokenu** pola. Jeśli to pole pozostanie puste, usługi Azure AD uwzględnione tokenu elementu nośnego OAuth wydawane z usługi Azure AD z każdym żądaniem. Aplikacje, które korzystają z usługi Azure AD jako dostawcy tożsamości można sprawdzić poprawność tej usługi Azure AD — wystawiony token.
8. Kliknij przycisk **Testuj połączenie** przycisk, aby mieć Azure Active Directory podejmować próby nawiązania połączenia z punktem końcowym Standard SCIM. Jeśli zakończy się niepowodzeniem, informacje o błędzie jest wyświetlany.  
9. Jeśli próbuje nawiązać połączenie z odnieść sukces aplikacji, kliknij przycisk **Zapisz** można zapisać poświadczeń administratora.
10. W **mapowania** sekcji, istnieją dwa zestawy można wybierać mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługi Azure Active Directory do swojej aplikacji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania użytkowników i grup w Twojej aplikacji dla operacji aktualizowania. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

    >[!NOTE]
    >Opcjonalnie można wyłączyć synchronizację grupy obiektów, wyłączając mapowania "groups". 

11. W obszarze **ustawienia**, **zakres** pole definiuje, które użytkownicy i grupy są synchronizowane. Wybieranie "Synchronizuj tylko przypisanych użytkowników i grup" (zalecane) będzie zsynchronizować tylko użytkownicy i grupy przypisane w **użytkowników i grup** kartę.
12. Po zakończeniu konfiguracji, zmień **stanie aprowizacji** do **na**.
13. Kliknij przycisk **Zapisz** można uruchomić usługi aprowizacji usługi Azure AD. 
14. Jeśli synchronizacja tylko przypisanych użytkowników i grup (zalecane), pamiętaj o wybraniu **użytkowników i grup** karcie, a następnie Przypisz użytkowników i/lub grup, które chcesz synchronizować.

Po rozpoczęciu synchronizacji początkowej, można użyć **dzienniki inspekcji** kartę do monitorowania postępu, który pokazuje każdą akcję wykonaną przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

>[!NOTE]
>Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, tak długo, jak usługa jest uruchomiona. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Tworzenie własnego rozwiązania inicjowania obsługi administracyjnej dla dowolnej aplikacji
Standard SCIM usługi sieci web, które interfejsy są tworzone za pomocą usługi Azure Active Directory, aby umożliwić jednego użytkownika do logowania jednokrotnego i automatycznego inicjowania obsługi administracyjnej dla praktycznie dowolnej aplikacji, która zawiera użytkownika aprowizujący interfejs API REST lub protokołu SOAP.

Poniżej przedstawiono, jak to działa:

1. Usługa Azure AD udostępnia bibliotekę wspólnej infrastruktury języka o nazwie [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). System integratorzy i programiści można użyć tej biblioteki do tworzenia i wdrażania punkt końcowy usługi sieci web opartych na użyciu SCIM możliwość połączenia usługi Azure AD do magazynu tożsamości dowolnej aplikacji.
2. Mapowania są implementowane w usłudze sieci web, aby zamapować schemat standardowy użytkownika na schemat użytkownika oraz Protokół wymagany przez aplikację.
3. Adres URL punktu końcowego jest zarejestrowany w usłudze Azure AD jako część niestandardową aplikację w galerii aplikacji.
4. Użytkownicy i grupy są przypisane do tej aplikacji w usłudze Azure AD. Podczas przypisywania są umieszczane w kolejce do synchronizacji z aplikacji docelowej. Proces synchronizacji, obsługa kolejki jest uruchamiane co 40 minut.

### <a name="code-samples"></a>Przykłady kodu
Aby ułatwić ten proces, [przykłady kodu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) są pod warunkiem, że tworzenie punktu końcowego usługi sieci web Standard SCIM i pokazują automatycznej aprowizacji. Przykład jest dostawcę, który przechowuje plik wierszy z wartościami rozdzielonymi przecinkami reprezentująca użytkowników i grup.  Druga to dostawcy, który działa w usłudze Amazon Web Services tożsamość i zarządzanie dostępem.  

**Wymagania wstępne**

* Visual Studio 2013 lub nowszy
* [Zestaw Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/)
* Windows maszyny, która obsługuje struktury programu ASP.NET 4.5 ma być używany jako punkt końcowy Standard SCIM. Ta maszyna musi być dostępny z poziomu chmury
* [Subskrypcja platformy Azure w wersji próbnej lub licencjonowanej wersji programu Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Wprowadzenie
Najłatwiejszym sposobem realizowania Standard SCIM punktu końcowego, który może akceptować żądania alokacji z usługi Azure AD jest, aby skompilować i wdrożyć przykładowy kod, który wyprowadza aprowizowanych użytkowników do pliku wartości rozdzielanych przecinkami (CSV).

**Aby utworzyć punkt końcowy Standard SCIM próbki:**

1. Pobierz pakiet przykładowy kod w [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Rozpakuj pakiet i umieść go na komputerze Windows w lokalizacji takiej jak C:\AzureAD-BYOA-Provisioning-Samples\.
3. W tym folderze Uruchom projekt FileProvisioning\Host\FileProvisioningService.csproj w programie Visual Studio.
4. Wybierz **Narzędzia > Menedżer pakietów NuGet > Konsola Menedżera pakietów**i wykonaj następujące polecenia w projekcie FileProvisioningService do rozpoznawania odwołań rozwiązania:
  ```` 
   Update-Package -Reinstall
  ````
5. Skompiluj projekt FileProvisioningService.
6. Uruchamianie aplikacji wiersza polecenia w Windows (jako Administrator) i używanie **cd** polecenia Zmień katalog na Twoje **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**folderu.
7. Uruchom następujące polecenie, zastępując < adres ip > IP adresu lub nazwy domeny komputera Windows:
  ````   
   FileSvc.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. W Windows w obszarze **Windows Ustawienia > sieci & ustawień internetowych**, wybierz opcję **zapory Windows > Ustawienia zaawansowane**i Utwórz **reguły ruchu przychodzącego** , Umożliwia dostęp ruchu przychodzącego do portu 9000.
9. Jeśli komputer Windows jest za routerem, router musi być skonfigurowany do wykonywania tłumaczeń dostępu do sieci między jego port 9000, który jest uwidaczniany w Internecie i port 9000 na komputerze Windows. Ta konfiguracja jest wymagana dla usługi Azure AD mieć możliwość dostępu do tego punktu końcowego w chmurze.

**Aby zarejestrować punkt końcowy Standard SCIM przykładowych w usłudze Azure AD:**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com). 
2. Przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**i wybierz **nowej aplikacji > wszystkie > Aplikacja spoza galerii**.
3. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Dodaj** ikonę, aby utworzyć obiekt aplikacji. Utworzony obiekt aplikacji jest przeznaczony do reprezentowania aplikacji docelowej, można będzie aprowizacji i implementowanie logowania jednokrotnego dla i nie tylko punkt końcowy Standard SCIM.
4. Z wyświetlonego ekranu wybierz **aprowizacji** karty w lewej kolumnie.
5. W **inicjowania obsługi trybu** menu, wybierz opcję **automatyczne**.
    
  ![][2]
  *Rysunek 4: Konfigurowanie aprowizacji w witrynie Azure portal*
    
6. W **adres URL dzierżawy** wprowadź adres URL i port punktu końcowego usługi Standard SCIM udostępnianych przez internet. Wpis jest podobny do http://testmachine.contoso.com:9000 lub http://<ip-address>:9000/, gdzie < adres ip > jest internet narażona adresu IP.  
7. Jeśli punkt końcowy Standard SCIM wymaga tokenu elementu nośnego OAuth od wystawcy innych niż Usługa Azure AD, następnie skopiuj wymagany token elementu nośnego OAuth do opcjonalnego **klucz tajny tokenu** pola. Jeśli to pole pozostanie puste, usługi Azure AD będą zawierać tokenu elementu nośnego OAuth wydawane z usługi Azure AD z każdym żądaniem. Aplikacje, które korzystają z usługi Azure AD jako dostawcy tożsamości można sprawdzić poprawność tej usługi Azure AD — wystawiony token.
8. Kliknij przycisk **Testuj połączenie** przycisk, aby mieć Azure Active Directory podejmować próby nawiązania połączenia z punktem końcowym Standard SCIM. Jeśli zakończy się niepowodzeniem, informacje o błędzie jest wyświetlany.  
9. Jeśli próbuje nawiązać połączenie z odnieść sukces aplikacji, kliknij przycisk **Zapisz** można zapisać poświadczeń administratora.
10. W **mapowania** sekcji, istnieją dwa zestawy można wybierać mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługi Azure Active Directory do swojej aplikacji. Atrybuty wybrany jako **zgodne** właściwości są używane do dopasowania użytkowników i grup w Twojej aplikacji dla operacji aktualizowania. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.
11. W obszarze **ustawienia**, **zakres** pole definiuje, które użytkownicy i grupy są synchronizowane. Wybieranie "Synchronizuj tylko przypisanych użytkowników i grup" (zalecane) będzie zsynchronizować tylko użytkownicy i grupy przypisane w **użytkowników i grup** kartę.
12. Po zakończeniu konfiguracji, zmień **stanie aprowizacji** do **na**.
13. Kliknij przycisk **Zapisz** można uruchomić usługi aprowizacji usługi Azure AD. 
14. Jeśli synchronizacja tylko przypisanych użytkowników i grup (zalecane), pamiętaj o wybraniu **użytkowników i grup** karcie, a następnie Przypisz użytkowników i/lub grup, które chcesz synchronizować.

Po rozpoczęciu synchronizacji początkowej, można użyć **dzienniki inspekcji** kartę do monitorowania postępu, który pokazuje każdą akcję wykonaną przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania aprowizacji dzienniki usługi Azure AD, zobacz [raportowanie na inicjowanie obsługi administracyjnej konta użytkownika automatyczne](../active-directory-saas-provisioning-reporting.md).

W ostatnim kroku weryfikacji próbki jest można otworzyć pliku TargetFile.csv w folderze \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug na komputerze Windows. Po jego uruchomieniu procesu aprowizacji ten plik zawiera szczegółowe informacje o wszystkich przypisane i aprowizacji użytkowników i grup.

### <a name="development-libraries"></a>Biblioteki programistyczne
Aby opracować własne usługi sieci web, który jest zgodny ze specyfikacją Standard SCIM, najpierw zapoznać się z następującymi bibliotekami, które są obsługiwane przez firmę Microsoft, aby przyspieszyć proces programowania: 

1. Wspólne biblioteki Language Infrastructure (CLI) są oferowane do użytku z językami oparte na tej infrastruktury, takich jak C#. Jedną z tych bibliotek [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklaruje interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IProvider, pokazane na poniższej ilustracji: A dla deweloperów przy użyciu biblioteki będzie implementują ten interfejs, za pomocą klasy może być określone, objęty dostawcy. Biblioteki umożliwiają deweloperom wdrażanie usługi sieci web, który jest zgodny ze specyfikacją Standard SCIM. Usługa sieci web mogą być albo hostowane w Internet Information Services lub zestawu Common Language Infrastructure dowolnego pliku wykonywalnego. Żądanie jest tłumaczony na wywołania metody dostawcy, które będzie zaprogramowane przez projektanta do pracy na niektórych magazynu tożsamości.
  
  ![][3]
  
2. [Express route obsługi](http://expressjs.com/guide/routing.html) zostały wprowadzone dostępne do przetwarzania node.js żądania obiektów reprezentująca wywołania (zgodnie z definicją w specyfikacji Standard SCIM), usługą sieci web środowiska node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Tworzenie punktu końcowego niestandardowe Standard SCIM
Korzystanie z bibliotek interfejsu wiersza polecenia, deweloperzy korzystający z tych bibliotek mogą hostować usługi w ramach zestawu Common Language Infrastructure dowolnego pliku wykonywalnego lub w Internet Information Services. Poniżej przedstawiono przykładowy kod dla innej usługi w ramach zestawu pliku wykonywalnego, pod adresem hostingu http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Ta usługa musi mieć HTTP adres i serwerem uwierzytelniania certyfikat których główny urząd certyfikacji jest jednym z następujących nazw: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Certyfikat uwierzytelniania serwera można powiązać z portem na hoście Windows za pomocą narzędzia powłoki sieciowej: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

W tym miejscu wartość podana dla argumentu certhash jest odcisk palca certyfikatu, gdy wartość podana dla appid argument jest umownym identyfikatorem globalnie unikatowa.  

Aby hostować usługi w Internet Information Services, deweloper będzie kompilacji zestaw CLA kodu biblioteki za pomocą klasy o nazwie uruchamiania w domyślnej przestrzeni nazw zestawu.  Poniżej przedstawiono przykład takiego klasy: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Obsługa punktu końcowego uwierzytelniania
Żądania z usługi Azure Active Directory zawierać tokenu elementu nośnego OAuth 2.0.   Każda usługa odbiera żądanie, powinna uwierzytelniać wystawcy jako znajdujące się w imieniu z oczekiwaną dzierżawą usługi Azure Active Directory w celu uzyskania dostępu do usługi sieci web usługi Azure Active Directory Graph usługi Azure Active Directory.  W tokenie wystawca jest identyfikowany przez oświadczenie iss, takich jak "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  W tym przykładzie adres bazowy wartość oświadczenia https://sts.windows.net, identyfikuje usługi Azure Active Directory jako wystawca, podczas gdy względny adres segmentu, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, znajduje się na unikatowy identyfikator dzierżawy usługi Azure Active Directory w imieniu którego token został wystawiony.  Jeśli token został wystawiony na potrzeby uzyskiwania dostępu do usługi sieci web usługi Azure Active Directory Graph, identyfikator tej usługi, 00000002-0000-0000-c000-000000000000, powinien być w wartości oświadczenia aud tokenu.  

Deweloperzy korzystający z biblioteki CLA, obsługiwane przez firmę Microsoft do tworzenia usługi Standard SCIM może uwierzytelnić żądania z usługi Azure Active Directory przy użyciu pakietu Microsoft.Owin.Security.ActiveDirectory, wykonując następujące czynności: 

1. W dostawcy implementują właściwość Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior przez on zwrócić metoda wywoływana zawsze wtedy, gdy usługa jest uruchomiona: 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Dodaj następujący kod do tej metody, aby każde żądanie do dowolnych punktów końcowych usługi uwierzytelniony jako posiadających token wystawiony przez usługę Azure Active Directory w imieniu określonego dzierżawcę, aby uzyskać dostęp do usługi sieci web Azure AD Graph: 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Schemat użytkowników i grup
Usługa Azure Active Directory można udostępnić dwa typy zasobów w usługach sieci web Standard SCIM.  Te typy zasobów to użytkowników i grup.  

Zasoby użytkowników są identyfikowane przez identyfikator schematu, "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", który jest dostępny w tej specyfikacji protokołu: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Domyślne mapowanie atrybutów użytkowników w usłudze Azure Active Directory z atrybutami "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" zasobów znajduje się w tabeli 1 poniżej.  

Grupy zasobów są identyfikowane przez identyfikator schematu http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabela 2, poniżej przedstawiono domyślne mapowanie atrybutów grupy w usłudze Azure Active Directory w atrybutach http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group zasobów.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Domyślne mapowanie atrybutu użytkownika
| Użytkownika usługi Azure Active Directory | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktywne |
| displayName |displayName |
| Facsimile-TelephoneNumber |wartość phoneNumbers [typ eq "faksu"] |
| givenName |name.givenName |
| Stanowisko |tytuł |
| poczta |wiadomości e-mail [typ eq "Praca"] .value |
| mailNickname |externalId |
| menedżer |menedżer |
| Telefon komórkowy |wartość phoneNumbers [eq wpisz "wyraz mobile"] |
| Identyfikator obiektu |ID |
| postalCode |.postalCode adresy [typ eq "Praca"] |
| proxy-Addresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |
| physical-Delivery-OfficeName |adresy [Wpisz eq "other"]. Sformatowany |
| Adres |.streetAddress adresy [typ eq "Praca"] |
| nazwisko |name.familyName |
| Numer telefonu |wartość phoneNumbers [typ eq "Praca"] |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Domyślne mapowanie atrybutów grupy
| Grupa usługi Azure Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| poczta |wiadomości e-mail [typ eq "Praca"] .value |
| mailNickname |displayName |
| członkowie |członkowie |
| Identyfikator obiektu |ID |
| proxyAddresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |

## <a name="user-provisioning-and-de-provisioning"></a>Inicjowanie obsługi użytkowników i anulowanie obsługi.
Poniższa ilustracja przedstawia komunikatów usługi Azure Active Directory i wysyła do usługi Standard SCIM do zarządzania cyklem życia użytkowników w innym magazynie tożsamości. Diagram pokazuje również, jak zaimplementować przy użyciu biblioteki interfejsu wiersza polecenia usługi Standard SCIM udostępniała przez firmę Microsoft do tworzenia takich usług tłumaczenie wywołaniami metod dostawcę te żądania.  

![][4]
*Rysunek 5: Inicjowanie obsługi użytkowników i cofanie aprowizacji sekwencji*

1. Usługa Azure Active Directory kwerendę usługi dla użytkownika z wartością atrybutu externalId dopasowania wartość atrybutu mailNickname użytkownika w usłudze Azure AD. Kwerenda jest wyrażona jako żądania protokołu HTTP (Hypertext Transfer), takie jak następująco, w którym jyoung znajduje się przykład mailNickname użytkownika w usłudze Azure Active Directory: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczony na wywołanie do metody zapytania dostawcy usług.  Podpis tej metody jest następujący: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Poniżej przedstawiono definicję interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  W poniższym przykładzie zapytanie dla użytkownika z danej wartości dla atrybutu externalId wartości Argumenty przekazane do metody zapytania są: 
  * parameters.AlternateFilters.Count: 1
  * Parametry. AlternateFilters.ElementAt(0). AttributePath: "externalId"
  * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
  * Parametry. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Jeśli odpowiedzi na zapytanie do usługi sieci web dla użytkownika z externalId wartość atrybutu, który pasuje do wartości atrybut mailNickname użytkownika nie zwróci żadnych użytkowników, następnie usługi Azure Active Directory żądań, usługę aprowizacji użytkownika odpowiadającego w usłudze Azure Active Directory.  Poniżej przedstawiono przykład takiego żądania: 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  Biblioteki Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM przekłada to żądanie do wywołania metody Create, dostawcy usług.  Metoda tworzenia ma podpis: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  W żądaniu na aprowizację użytkownika wartość argumentu zasobów jest wystąpieniem Microsoft.SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser zdefiniowane w bibliotece Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Jeśli żądanie obsługi administracyjnej użytkownika zakończy się powodzeniem, następnie implementacji metody powinien zwrócić wystąpienia Microsoft.SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser z wartością identyfikatora ustawioną na unikatowy identyfikator użytkownika, które użytkownicy nowo aprowizowanych.  

3. Do zaktualizowania użytkownika istnieje w magazynie tożsamości przez standard SCIM, usługi Azure Active Directory przechodzi przez zażądanie bieżący stan tego użytkownika z usługi z żądaniem, takie jak: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  W usłudze utworzone przy użyciu biblioteki Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM żądanie jest tłumaczony na wywołanie metody pobierania dostawcy usług.  Poniżej znajduje się sygnatura metody pobierania: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  W przykładzie żądanie, aby pobrać bieżący stan użytkownika wartości właściwości obiektu, podana jako wartość argumentu parametry są następujące: 
  
  * Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Jeśli atrybut odwołania ma zostać zaktualizowany, a następnie usługi Azure Active Directory korzysta z usługi, aby ustalić, czy bieżąca wartość atrybutu odwołania magazynu tożsamości fronted przez usługę już odpowiada wartości danego atrybutu w usłudze Azure Active Katalog. Dla użytkowników to jedyny atrybut, którego bieżącą wartość jest wysyłane zapytanie w ten sposób jest to atrybut menedżera. Oto przykładowe żądanie, aby określić, czy atrybut menedżera obiektu dany użytkownik ma obecnie określonej wartości: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  Wartość parametru zapytania atrybutów, "ID" oznacza, że jeśli obiekt użytkownika istnieje, która spełnia wyrażenie podane jako wartość parametru zapytania filtru, a następnie usługa oczekuje na odpowiedź z "urn: ietf:params:scim:schemas:core:2.0: Zasób użytkownika"lub"urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", z uwzględnieniem tylko wartości atrybutu"ID"tego zasobu.  Wartość **identyfikator** atrybutu jest znany do osoby zgłaszającej żądanie. Jest on zawarty w wartości parametru zapytania filtru; Celem pyta jest faktycznie żądanie minimalny reprezentacja zasobu spełniającej wyrażenie filtru w celu wskazania, czy istnieje takiego obiektu.   

  Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczony na wywołanie do metody zapytania dostawcy usług. Wartość właściwości obiektu, podana jako wartość argumentu parametry są następujące: 
  
  * parameters.AlternateFilters.Count: 2
  * Parametry. AlternateFilters.ElementAt(x). AttributePath: "ID"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * Parametry. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * Parametry. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * Parametry. RequestedAttributePaths.ElementAt(0): "ID"
  * Parametry. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

  W tym miejscu wartość indeksu x może mieć wartość 0 i wartość y indeks może być 1, lub wartość x może mieć wartość 1 i wartość y, może być 0, w zależności od kolejności wyrażeń parametr zapytania filtru.   

5. Oto przykład żądania z usługi Azure Active Directory z usługą Standard SCIM do zaktualizowania użytkownika: 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  Biblioteki Microsoft Common Language Infrastructure dotyczące implementowania usługi Standard SCIM przetłumaczy żądania do wywołania metody aktualizacji dostawcy usług. Oto podpis metody aktualizacji: 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    W przykładzie żądania do zaktualizowania użytkownika obiektu podana jako wartość argumentu poprawki ma wartości tych właściwości: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest jako PatchRequest2). Operations.Count: 1
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.Count: 1
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Odwołanie: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Wartość: 2819c223-7f76-453a-919d-413861904646

6. Aby anulować aprowizację użytkowników z magazynu tożsamości przez usługi Standard SCIM, usługi Azure AD wysyła żądanie takich jak: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Jeśli usługa została skompilowana przy użyciu bibliotek Common Language Infrastructure obsługiwane przez firmę Microsoft do implementowania usługi Standard SCIM, żądanie jest tłumaczona na wywołanie metody usuwania, które dostawcy usług.   Ta metoda ma podpis: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  Obiekt udostępniany jako wartość argumentu resourceIdentifier ma wartości tych właściwości w przykładzie żądanie, aby anulować aprowizację użytkownika: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Grupa aprowizację i cofanie aprowizacji
Poniższa ilustracja przedstawia komunikaty które wysyła AcD platformy Azure z usługą Standard SCIM do zarządzania cyklem życia grupy w innym magazynie tożsamości.  Te komunikaty różnią się od komunikatów odnoszących się do użytkowników na trzy sposoby: 

* Schemat zasób grupy jest identyfikowany jako `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Żądania pobrania grupy stanowić atrybut elementów członkowskich mają być wykluczone z dowolnego zasobu w odpowiedzi na żądanie.  
* Żądania, aby ustalić, czy jest atrybut odwołania, ma określoną wartość są żądania o atrybucie elementów członkowskich.  

![][5]
*Rysunek 6: Grupa aprowizację i anulowanie obsługi sekwencji*

## <a name="related-articles"></a>Pokrewne artykuły:
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](../active-directory-apps-index.md)
* [Automatyzowanie użytkownika aprowizacji/Deprovisioning do aplikacji SaaS](../active-directory-saas-app-provisioning.md)
* [Dostosowywanie mapowań atrybutów dla aprowizacji użytkowników](../active-directory-saas-customizing-attribute-mappings.md)
* [Pisanie wyrażeń do mapowania atrybutów](../active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtrów określania zakresu na potrzeby aprowizacji użytkownika](../active-directory-saas-scoping-filters.md)
* [Powiadomienia z Aprowizacją kont](../active-directory-saas-app-provisioning.md)
* [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
