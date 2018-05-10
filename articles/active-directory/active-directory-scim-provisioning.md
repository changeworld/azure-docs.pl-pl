---
title: Automatyzowanie Inicjowanie obsługi aplikacji w usłudze Azure Active Directory przy użyciu SCIM | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory mogą automatycznie obsługiwać użytkowników i grup do aplikacji lub tożsamości magazynu, który jest fronted przez usługę sieci web przy użyciu interfejsu zdefiniowane w specyfikacji protokołu SCIM
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 19a1ae7ae7acc6fe09a529dd174363735343027e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Przy użyciu systemu do innej domeny zarządzania tożsamościami do automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji

## <a name="overview"></a>Przegląd
Azure Active Directory (Azure AD), mogą automatycznie obsługiwać użytkowników i grup do aplikacji lub tożsamości magazynu, który jest fronted przez usługę sieci web przy użyciu interfejsu zdefiniowanych w [systemu dla protokołu zarządzania tożsamości między domenami (SCIM) 2.0 Specyfikacja](https://tools.ietf.org/html/draft-ietf-scim-api-19). Usługa Azure Active Directory mogą wysyłać żądania do tworzenia, modyfikowania lub usuwania przypisane użytkowników i grup z usługą sieci web. Usługa sieci web może dokonywać translacji te żądania do operacji w magazynie docelowym tożsamości. 

![][0]
*Rysunek 1: Inicjowanie obsługi administracyjnej z usługi Azure Active Directory, do magazynu tożsamości za pomocą usługi sieci web*

Ta możliwość może służyć w połączeniu z możliwością "Przynieś własne aplikacji" w usłudze Azure AD do włączenia logowania jednokrotnego i użytkownika automatyczne Inicjowanie obsługi administracyjnej dla aplikacji, które zapewniają lub są fronted przez usługę sieci web SCIM.

Istnieją dwa przypadki użycia przy użyciu SCIM w usłudze Azure Active Directory:

* **Inicjowanie obsługi użytkowników i grup do aplikacji, które obsługują SCIM** aplikacji, które obsługują SCIM 2.0 i używają tokenów elementu nośnego OAuth dla działania uwierzytelniania w usłudze Azure AD bez konfiguracji.
* **Tworzenie rozwiązania inicjowania obsługi administracyjnej dla aplikacji, która obsługę innych oparty na interfejsach API** dla aplikacji z systemem innym niż SCIM, można utworzyć punktu końcowego SCIM translacji przez punkt końcowy usługi Azure AD SCIM i jakiegokolwiek interfejsu API obsługuje aplikacji Inicjowanie obsługi użytkowników. Ułatwiają tworzenie punktu końcowego SCIM, ma wspólnej infrastruktury języka (CLI) bibliotek oraz przykłady kodu, które pokazują, jak Podaj punkt końcowy SCIM i tłumaczenie SCIM wiadomości.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Inicjowanie obsługi użytkowników i grup do aplikacji, które obsługują SCIM
Usługi Azure AD można skonfigurować do automatycznego należy przypisać użytkowników i grup do aplikacji, które implementują [systemu do zarządzania tożsamościami międzydomenowego 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) usługi sieci web i zaakceptować tokenów elementu nośnego OAuth dla uwierzytelniania. W specyfikacji SCIM 2.0 aplikacji musi spełniać następujące wymagania:

* Obsługuje tworzenie użytkowników i grupy, zgodnie z sekcji 3.3 protokołu SCIM.  
* Obsługa modyfikowania użytkowników i/lub grup z żądaniami poprawki zgodnie z sekcji 3.5.2 protokołu SCIM.  
* Obsługuje pobieranie znanych zasobów zgodnie z sekcji 3.4.1 protokołu SCIM.  
* Obsługa zapytań użytkowników i grupy, zgodnie z sekcji 3.4.2 protokołu SCIM.  Domyślnie użytkownicy są proszeni przez externalId i grup są żądanych przez Nazwa wyświetlana.  
* Obsługa zapytań użytkownika według Identyfikatora i przez Menedżera zgodnie z sekcji 3.4.2 protokołu SCIM.  
* Obsługa zapytań grup według Identyfikatora i przez członka zgodnie z sekcji 3.4.2 protokołu SCIM.  
* Akceptuje tokenów elementu nośnego OAuth dla autoryzacji zgodnie z sekcji 2.1 protokołu SCIM.

Skontaktuj się z dostawcą aplikacji lub dokumentacji dostawcy aplikacji dla instrukcji zgodność z tych wymagań.

### <a name="getting-started"></a>Wprowadzenie
Aplikacje, które obsługują profilu SCIM opisane w tym artykule można podłączyć do usługi Azure Active Directory za pomocą funkcji "z systemem innym niż galerii aplikacji" w galerii aplikacji usługi Azure AD. Po nawiązaniu połączenia usługi Azure AD uruchamia proces synchronizacji co 20 minut, gdzie wysyła zapytanie do punktu końcowego SCIM aplikacji przypisanych użytkowników i grup i tworzy lub modyfikuje je zgodnie z szczegółów przypisania.

**Aby połączyć aplikację obsługującą SCIM:**

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 
2. Przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**i wybierz **nowej aplikacji > wszystkie > Non galerii aplikacji**.
3. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Dodaj** ikonę, aby utworzyć obiekt aplikacji.
    
  ![][1]
  *Rysunek 2: Galerii aplikacji usługi Azure AD*
    
4. Na ekranie wynikowy wybierz **inicjowania obsługi administracyjnej** kartę w lewej kolumnie.
5. W **inicjowania obsługi trybu** menu, wybierz opcję **automatyczne**.
    
  ![][2]
  *Rysunek 3: Konfigurowanie inicjowania obsługi w portalu Azure*
    
6. W **adres URL dzierżawy** wprowadź adres URL punktu końcowego SCIM aplikacji. Przykład: https://api.contoso.com/scim/v2/
7. Jeśli punkt końcowy SCIM wymaga tokenu elementu nośnego OAuth od wystawcy innego niż Azure AD, następnie skopiuj wymagany token elementu nośnego OAuth do opcjonalnego **klucz tajny tokenu** pola. Jeśli to pole pozostanie puste, usługi Azure AD uwzględnione tokenu elementu nośnego OAuth wystawione przez usługi Azure AD z każdym żądaniem. Aplikacje, które używają usługi Azure AD jako dostawca tożsamości może sprawdzić poprawność tej usługi Azure AD-wystawiony token.
8. Kliknij przycisk **Testuj połączenie** przycisk, aby podejmować próby nawiązania połączenia z punktem końcowym SCIM usługi Azure Active Directory. W przypadku awarii próby, wyświetlane informacje o błędzie.  
9. Jeśli próby połączenia się pomyślnie aplikacji, należy kliknąć **zapisać** można zapisać poświadczeń administratora.
10. W **mapowania** sekcji, istnieją dwa zestawy wybieranych mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Zaznacz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługą Azure Active Directory do aplikacji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania użytkowników i grup w aplikacji dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

    >[!NOTE]
    >Opcjonalnie możesz wyłączyć synchronizację obiektów grupy przez wyłączenie mapowania "grupy". 

11. W obszarze **ustawienia**, **zakres** pola definiuje, które użytkownicy i grupy są synchronizowane. Wybranie "Synchronizacji tylko przypisane użytkowników i grup" (zalecane) będzie tylko synchronizować użytkownicy i grupy przypisane w **użytkowników i grup** kartę.
12. Po zakończeniu konfiguracji zmienić **stan inicjowania obsługi administracyjnej** do **na**.
13. Kliknij przycisk **zapisać** można uruchomić usługi Azure AD, inicjowania obsługi usługi. 
14. Jeśli synchronizacja przypisana tylko użytkownicy i grupy (zalecane), należy wybrać **użytkowników i grup** karcie i Przypisz użytkowników i/lub grup, które chcesz wykonać synchronizację.

Po rozpoczęciu synchronizacji początkowej, można użyć **dzienniki inspekcji** kartę do monitorowania postępu, który pokazuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji. Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>Synchronizacji początkowej zajmuje więcej czasu wykonywania niż kolejne synchronizacje, występujące co około 20 minut, tak długo, jak usługa jest uruchomiona. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Tworzenie rozwiązania inicjowania obsługi administracyjnej dla dowolnej aplikacji
Tworząc interfejsy usługi sieci web SCIM z usługi Azure Active Directory, można włączyć pojedynczego użytkownika logowania jednokrotnego i automatyczne Inicjowanie obsługi praktycznie dowolnej aplikacji, która zawiera użytkownika inicjowania obsługi interfejsu API REST lub protokołu SOAP.

Oto jak to działa:

1. Usługa Azure AD zapewnia o nazwie biblioteki wspólnej infrastruktury języka [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Deweloperów i integratorów systemów. platforma można użyć tej biblioteki, możesz utworzyć i wdrożyć punkt końcowy usługi sieci web opartych na SCIM może nawiązywać połączenia usługi Azure AD z magazynu tożsamości dowolnej aplikacji.
2. Mapowania są implementowane w usłudze sieci web do mapowania schematu użytkowników standardowych użytkowników schematu i protokół wymagane przez aplikację.
3. Końcowy adres URL jest zarejestrowany w usłudze Azure AD w ramach niestandardowych aplikacji w galerii aplikacji.
4. Użytkownicy i grupy są przypisane do tej aplikacji w usłudze Azure AD. Po przypisania są umieszczane w kolejce do synchronizacji w aplikacji docelowej. Proces synchronizacji obsługi kolejki jest uruchamiana co 20 minut.

### <a name="code-samples"></a>Przykłady kodu
Tego łatwiejsze, procesu to zbiór [przykłady kodu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) pod warunkiem że utworzyć SCIM punkt końcowy usługi sieci web i Wykaż, automatyczne udostępnianie. Przykład jest dostawcy, który przechowuje plik z wierszami z wartościami rozdzielonymi przecinkami reprezentująca użytkowników i grup.  Druga to dostawcy, który działa na usługi Amazon Web Services tożsamość i zarządzanie dostępem.  

**Wymagania wstępne**

* Visual Studio 2013 lub nowszy
* [Zestaw Azure SDK dla platformy .NET](https://azure.microsoft.com/downloads/)
* Windows urządzenia, które obsługuje struktury programu ASP.NET 4.5 do użycia jako punkt końcowy SCIM. Tego komputera muszą być dostępne z chmury
* [Subskrypcja platformy Azure w wersji próbnej lub licencjonowanej wersji programu Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* Przykład Amazon AWS wymaga bibliotek [usług AWS narzędzi dla programu Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Aby uzyskać więcej informacji zobacz plik README, uwzględnionych w próbce.

### <a name="getting-started"></a>Wprowadzenie
Najprostszym sposobem wykonania SCIM punktu końcowego, który może zaakceptować żądania alokacji z usługi Azure AD jest do tworzenia i wdrażania przykładowy kod, który wyprowadza elastycznie użytkowników do pliku wartości rozdzielanych przecinkami (CSV).

**Aby utworzyć punkt końcowy SCIM próbki:**

1. Pobierz przykładowy kod w [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Rozpakuj pakiet i umieść go na komputerze z systemem Windows w lokalizacji, takich jak C:\AzureAD-BYOA-Provisioning-Samples\.
3. W tym folderze uruchom rozwiązania FileProvisioningAgent w programie Visual Studio.
4. Wybierz **Narzędzia > Menedżer pakietów biblioteki > konsoli Menedżera pakietów**i wykonaj następujące polecenia dla projektu FileProvisioningAgent można rozpoznać odwołań do rozwiązania:
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Skompiluj projekt FileProvisioningAgent.
6. Uruchamianie aplikacji wiersza polecenia w systemie Windows (jako Administrator), a następnie użyj **cd** polecenia Zmień katalog na Twojej **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** folder.
7. Uruchom następujące polecenie, zastępując < adres ip > IP adresu lub nazwy domeny komputera z systemem Windows:
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. W systemie Windows w obszarze **ustawienia systemu Windows > Sieć i Internet ustawienia**, wybierz pozycję **zapory systemu Windows > Zaawansowane ustawienia**i Utwórz **reguły ruchu przychodzącego** który Umożliwia przychodzący dostęp do portu 9000.
9. W przypadku komputera z systemem Windows za routerem, router musi być skonfigurowany do wykonywania tłumaczenia dostępu do sieci między portu 9000, który jest połączenie z Internetem i portu 9000 na komputerze z systemem Windows. Ta konfiguracja jest wymagana dla usługi Azure AD można było uzyskać dostępu do tego punktu końcowego w chmurze.

**Aby zarejestrować punkt końcowy SCIM przykładowych w usłudze Azure AD:**

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 
2. Przejdź do **usługi Azure Active Directory > aplikacje dla przedsiębiorstw**i wybierz **nowej aplikacji > wszystkie > Non galerii aplikacji**.
3. Wprowadź nazwę aplikacji, a następnie kliknij przycisk **Dodaj** ikonę, aby utworzyć obiekt aplikacji. Utworzony obiekt aplikacji jest przeznaczony do reprezentowania aplikacji docelowej może być inicjowania obsługi administracyjnej i implementowania rejestracji jednokrotnej dla, a nie tylko punkt końcowy SCIM.
4. Na ekranie wynikowy wybierz **inicjowania obsługi administracyjnej** kartę w lewej kolumnie.
5. W **inicjowania obsługi trybu** menu, wybierz opcję **automatyczne**.
    
  ![][2]
  *Rysunek 4: Konfigurowanie inicjowania obsługi w portalu Azure*
    
6. W **adres URL dzierżawy** wprowadź adres URL i port punktu końcowego SCIM dostępne za pośrednictwem Internetu. Wpis jest podobny do http://testmachine.contoso.com:9000 lub http://<ip-address>:9000/, gdzie < adres ip > jest internet narażona adresu IP.  
7. Jeśli punkt końcowy SCIM wymaga tokenu elementu nośnego OAuth od wystawcy innego niż Azure AD, następnie skopiuj wymagany token elementu nośnego OAuth do opcjonalnego **klucz tajny tokenu** pola. Jeśli to pole pozostanie puste, usługi Azure AD będą zawierać tokenu elementu nośnego OAuth wystawione przez usługi Azure AD z każdym żądaniem. Aplikacje, które używają usługi Azure AD jako dostawca tożsamości może sprawdzić poprawność tej usługi Azure AD-wystawiony token.
8. Kliknij przycisk **Testuj połączenie** przycisk, aby podejmować próby nawiązania połączenia z punktem końcowym SCIM usługi Azure Active Directory. W przypadku awarii próby, wyświetlane informacje o błędzie.  
9. Jeśli próby połączenia się pomyślnie aplikacji, należy kliknąć **zapisać** można zapisać poświadczeń administratora.
10. W **mapowania** sekcji, istnieją dwa zestawy wybieranych mapowań atrybutów: jeden dla obiektów użytkownika i jeden dla obiektów grupy. Zaznacz każdą z nich Przejrzyj atrybuty, które są synchronizowane z usługą Azure Active Directory do aplikacji. Atrybuty wybrany jako **pasujące** właściwości są używane do dopasowania użytkowników i grup w aplikacji dla operacji update. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.
11. W obszarze **ustawienia**, **zakres** pola definiuje, które użytkownicy i grupy są synchronizowane. Wybranie "Synchronizacji tylko przypisane użytkowników i grup" (zalecane) będzie tylko synchronizować użytkownicy i grupy przypisane w **użytkowników i grup** kartę.
12. Po zakończeniu konfiguracji zmienić **stan inicjowania obsługi administracyjnej** do **na**.
13. Kliknij przycisk **zapisać** można uruchomić usługi Azure AD, inicjowania obsługi usługi. 
14. Jeśli synchronizacja przypisana tylko użytkownicy i grupy (zalecane), należy wybrać **użytkowników i grup** karcie i Przypisz użytkowników i/lub grup, które chcesz wykonać synchronizację.

Po rozpoczęciu synchronizacji początkowej, można użyć **dzienniki inspekcji** kartę do monitorowania postępu, który pokazuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji. Aby uzyskać więcej informacji na temat usługi Azure AD, inicjowanie obsługi dzienników do odczytu, zobacz [raportowania na użytkownika automatyczne Inicjowanie obsługi konta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

Ostatnim krokiem podczas weryfikowania próbki jest można otworzyć pliku TargetFile.csv w folderze \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug na komputerze z systemem Windows. Po uruchomieniu procesu zastrzegania, ten plik zawiera szczegółowe informacje o całości przypisane i udostępnione użytkowników i grup.

### <a name="development-libraries"></a>Biblioteki programistyczne
Aby opracować własnej usługi sieci web, który jest zgodny ze specyfikacją SCIM, najpierw zapoznać się z następujących bibliotek obsługiwane przez firmę Microsoft w celu przyspieszenia procesu tworzenia: 

1. Wspólne biblioteki języka infrastruktury (CLI) dostępnych do użycia z językami oparte na infrastruktury, takich jak C#. Jeden z tych bibliotek [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklaruje interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IProvider, pokazane na poniższej ilustracji: A Developer przy użyciu bibliotek czy implementuje ten interfejs z klasy, która może być określone, objęty dostawcę. Biblioteki umożliwiają deweloperom wdrażanie usługi sieci web, który jest zgodny ze specyfikacją SCIM. Usługi sieci web może być obsługiwany albo w ramach Internetowe usługi informacyjne lub dowolnego pliku wykonywalnego zestawu wspólną infrastrukturę języka. Żądanie jest przetłumaczony na wywołania metody dostawcy, które mogłyby programowane przez dewelopera do działania na niektórych magazynu tożsamości.
  
  ![][3]
  
2. [Express obsługi trasy](http://expressjs.com/guide/routing.html) są dostępne na potrzeby analizowania node.js żądania obiekty reprezentujące wywołania (zgodnie z definicją w specyfikacji SCIM), wprowadzone do usługi sieci web node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Tworzenie punktu końcowego niestandardowych SCIM
Przy użyciu biblioteki interfejsu wiersza polecenia, deweloperzy przy użyciu tych bibliotek mogą być hostowane swoich usług w dowolnym pliku wykonywalnego zestawu wspólną infrastrukturę języka lub do internetowych usług informacyjnych. Poniżej przedstawiono przykładowy kod w celu hostowania usługi w zestawie pliku wykonywalnego, pod adresem http://localhost:9000: 

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

Ta usługa musi mieć HTTP adres i serwera uwierzytelniania certyfikat których główny urząd certyfikacji jest jednym z następujących nazw: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Certyfikat uwierzytelniania serwera może być powiązana z portu na hoście z systemem Windows przy użyciu narzędzia powłoki sieciowej: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

W tym miejscu wartość podana dla argumentu skrót certyfikatu jest odcisk palca certyfikatu, gdy wartość podana dla argumentu identyfikator appid jest umownym identyfikatorem globalnie unikatowy.  

Do obsługi usługi w usługach IIS, deweloper może kompilacji w zestawie CLA kod biblioteki z klasy o nazwie uruchamiania dla domyślnej przestrzeni nazw zestawu.  Oto przykład takiego klasy: 

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
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Obsługa punktu końcowego uwierzytelniania
Żądania z usługi Azure Active Directory zawierać tokenu elementu nośnego OAuth 2.0.   Każda usługa żądania odbierania powinna uwierzytelniać wystawca jako imieniu oczekiwanego dzierżawy usługi Azure Active Directory, do uzyskiwania dostępu do usługi sieci web Azure Active Directory Graph usługi Azure Active Directory.  W tokenie, wystawca jest identyfikowany przez oświadczenie iss, takich jak "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  W tym przykładzie adres podstawowy wartość oświadczenia https://sts.windows.net, identyfikuje usługi Azure Active Directory jako wystawca względnej dotyczy segmentu, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, znajduje się na unikatowy identyfikator dzierżawy usługi Azure Active Directory w imieniu którego token został wystawiony.  Jeśli token został wystawiony na uzyskiwanie dostępu do usługi sieci web Azure Active Directory Graph, identyfikator tej usługi, 00000002-0000-0000-c000-000000000000, powinien być w wartości oświadczenia lub tokenu.  

Deweloperzy przy użyciu bibliotek CLA obsługiwane przez firmę Microsoft do tworzenia usługi SCIM może uwierzytelnić żądania z usługi Azure Active Directory przy użyciu pakietu Microsoft.Owin.Security.ActiveDirectory, wykonując następujące czynności: 

1. U dostawcy należy zaimplementować właściwości Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior przez go zwracać metoda do wywołania po każdym uruchomieniu usługi: 

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

2. Dodaj następujący kod do tej metody, aby wszystkie żądania dla każdego z punktów końcowych usługi uwierzytelniony jako mając token wystawiony przez usługę Azure Active Directory w imieniu określonego dzierżawcę, aby uzyskać dostęp do usługi sieci web programu Azure AD Graph: 

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
Usługa Azure Active Directory można udostępnić dwa typy zasobów do SCIM usług sieci web.  Te typy zasobów są użytkowników i grup.  

Zasoby użytkownika są identyfikowane za pomocą identyfikatora schematu "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", który jest dostępny w tej specyfikacji protokołu: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Domyślne mapowanie atrybutów użytkowników w usłudze Azure Active Directory do atrybutów zasobów "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" podano w tabeli 1, poniżej.  

Grupy zasobów są identyfikowane za pomocą identyfikatora schematu http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabela 2, poniżej przedstawiono domyślne mapowanie atrybutów grup w usłudze Azure Active Directory w atrybutach http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group zasobów.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Domyślne mapowanie atrybutu użytkownika
| Azure użytkownika usługi Active Directory | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktywne |
| Nazwa wyświetlana |Nazwa wyświetlana |
| Facsimile-TelephoneNumber |.value phoneNumbers [eq typu "faks"] |
| givenName |name.givenName |
| Stanowisko |tytuł |
| Poczty |.value wiadomości e-mail [eq typu "Praca"] |
| mailNickname |externalId |
| Menedżer |Menedżer |
| Telefon komórkowy |.value phoneNumbers [eq typu "mobile"] |
| Identyfikator obiektu |id |
| postalCode |.postalCode adresów [eq typu "Praca"] |
| proxy-Addresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |
| physical-Delivery-OfficeName |adresy [Wpisz eq "other"]. Sformatowany |
| Adres |.streetAddress adresów [eq typu "Praca"] |
| nazwisko |name.familyName |
| Numer telefonu |.value phoneNumbers [eq typu "Praca"] |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Domyślne grupy atrybutów mapowanie
| Grupa usługi Azure Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| Nazwa wyświetlana |externalId |
| Poczty |.value wiadomości e-mail [eq typu "Praca"] |
| mailNickname |Nazwa wyświetlana |
| członkowie |członkowie |
| Identyfikator obiektu |id |
| proxyAddresses |wiadomości e-mail [Wpisz eq "other"]. Wartość |

## <a name="user-provisioning-and-de-provisioning"></a>Inicjowanie obsługi użytkowników i anulowanie obsługi.
Na poniższej ilustracji pokazano komunikatów wysyła usługi Azure Active Directory z usługą SCIM do zarządzania cyklem życia w innym magazynie tożsamości użytkownika. Na diagramie przedstawiono również sposób usługa SCIM implementowane przy użyciu biblioteki interfejsu wiersza polecenia obsługiwane przez firmę Microsoft do budowy, się, że takie usługi tłumaczenia te żądania na wywołania metod dostawcy.  

![][4]
*Rysunek 5: Użytkownik aprowizację i anulowanie obsługi sekwencji*

1. Usługa Azure Active Directory korzysta z usługi dla użytkownika z wartością atrybutu externalId dopasowywanie mailNickname wartość atrybutu użytkownika w usłudze Azure AD. Zapytanie jest wyrażony jako żądania protokołu HTTP (Hypertext Transfer), np. w tym przykładzie, w którym jyoung znajduje się przykład mailNickname użytkownika w usłudze Azure Active Directory: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Jeśli usługa został zbudowany przy użyciu bibliotek wspólną infrastrukturę języka obsługiwane przez firmę Microsoft dla implementacji usługi SCIM, żądanie jest przetłumaczony na wywołanie do metody zapytania dostawcy usług.  Podpis metody jest następujący: 
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
  W tym miejscu znajduje się definicja interfejsu Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
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

2. Jeśli odpowiedź na zapytanie do usługi sieci web dla użytkownika z wartością atrybutu externalId, która jest zgodna z wartością atrybutu mailNickname użytkownika nie zwraca żadnych użytkowników, następnie usługi Azure Active Directory żąda czy usługa udostępnić odpowiednie do tego użytkownika w usłudze Azure Active Directory.  Oto przykład takiego żądania: 
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
  Biblioteki wspólną infrastrukturę języka obsługiwane przez firmę Microsoft dla implementacji usługi SCIM przekłada to żądanie do wywołania metody Create dostawcy usług.  Metoda Create ma podpis: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  W żądaniu skierowanym do obsługi administracyjnej użytkownika wartość argumentu zasobów jest wystąpieniem Microsoft.SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser zdefiniowany w bibliotece Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Jeśli żądanie do udostępnienia użytkownik zakończy się powodzeniem, następnie implementacji metody powinien zwrócić wystąpienia Microsoft.SystemForCrossDomainIdentityManagement. Klasa Core2EnterpriseUser o wartość właściwości identyfikator ustawioną Unikatowy identyfikator nowo aprowizowanej użytkownika.  

3. Do zaktualizowania użytkownika istnieje w magazynie tożsamości przez SCIM, Azure Active Directory przechodzi przez zażądanie bieżący stan tego użytkownika z usługi z żądaniem, takich jak: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  W usłudze utworzony przy użyciu bibliotek wspólną infrastrukturę języka obsługiwane przez firmę Microsoft dla implementacji usługi SCIM żądanie jest przetłumaczony na wywołanie do metody pobierania dostawcy usług.  Oto podpis metody pobierania: 
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
  W tym przykładzie żądanie, aby pobrać bieżący stan użytkownika wartości właściwości obiektu dostarczonych jako wartość argumentu parametry są następujące: 
  
  * Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Jeśli atrybut odwołania ma zostać zaktualizowany, a następnie usługi Azure Active Directory wysyła zapytanie do usługi, aby określić, czy bieżąca wartość atrybutu odwołania magazynu tożsamości fronted przez usługę już jest zgodna z wartością tego atrybutu w usłudze Azure Active Katalog. W przypadku użytkowników jedyny atrybut, z których bieżąca wartość jest poddawany kwerendzie w ten sposób jest atrybutem menedżera. Oto przykład żądanie, aby ustalić, czy atrybut menedżera obiektu określonego użytkownika aktualnie ma określoną wartość: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  Wartość parametru zapytania atrybuty, "id" oznacza, że, jakby istniał obiektu użytkownika spełnia wyrażenie dostarczonych jako wartość parametru zapytania filtru, a następnie usługę powinien odpowiadać, podając "urn: ietf:params:scim:schemas:core:2.0: Zasób użytkownika"lub"urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", z uwzględnieniem tylko wartości atrybutu"id"tego zasobu.  Wartość **identyfikator** atrybutu jest znany obiekt żądający. Znajduje się on w wartości parametru zapytania filtru; Celem pyta jest rzeczywiście żądanie minimalnego reprezentacja zasobu spełniające wyrażenie filtru jako ze wskazaniem, czy istnieje takiego obiektu.   

  Jeśli usługa został zbudowany przy użyciu bibliotek wspólną infrastrukturę języka obsługiwane przez firmę Microsoft dla implementacji usługi SCIM, żądanie jest przetłumaczony na wywołanie do metody zapytania dostawcy usług. Wartość właściwości obiektu dostarczonych jako wartość argumentu parametry są następujące: 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * Parametry. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * Parametry. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * Parametry. RequestedAttributePaths.ElementAt(0): "id"
  * Parametry. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

  W tym miejscu wartość indeksu x może być równa 0 i wartość y indeksu może być równa 1, lub wartość x może być równa 1 i wartości y może mieć wartość 0, w zależności od kolejność wyrażenia parametru zapytania filtru.   

5. Oto przykład żądania z usługi Azure Active Directory z usługą SCIM do zaktualizowania użytkownika: 
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
  Implementowanie usług SCIM z bibliotekami wspólną infrastrukturę języka Microsoft przekłada żądania do wywołania metody aktualizacji dostawcy usług. Oto podpis metody aktualizacji: 
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
    W tym przykładzie żądania do zaktualizowania użytkownika dostarczony jako wartość argumentu poprawki obiekt ma wartości tych właściwości: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest jako PatchRequest2). Operations.Count: 1
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.Count: 1
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Odwołanie: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Wartość: 2819c223-7f76-453a-919d-413861904646

6. Aby anulować aprowizację użytkownika z tożsamości magazynu przez usługę SCIM, usługi Azure AD wysyła żądanie takie jak: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Jeśli usługa został zbudowany przy użyciu bibliotek wspólną infrastrukturę języka obsługiwane przez firmę Microsoft dla implementacji usługi SCIM, żądanie jest przetłumaczony na wywołanie do metody Delete dostawcy usług.   Ta metoda ma podpis: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  Obiekt udostępniany jako wartość argumentu resourceIdentifier ma wartości tych właściwości w tym przykładzie żądanie, aby anulować aprowizację użytkownika: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Grupy aprowizację i anulowanie obsługi
Na poniższej ilustracji pokazano komunikatów wysyła Azure AcD z usługą SCIM do zarządzania cyklem życia grupy w innym magazynie tożsamości.  Te komunikaty różnią się od komunikaty dotyczące użytkowników na trzy sposoby: 

* Schemat zasób grupy zostanie zidentyfikowana jako `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Żądania pobrania grup stanowić atrybut elementów członkowskich mają być wykluczone z dowolnego zasobu w odpowiedzi na żądanie.  
* Żądania, aby ustalić, czy jest atrybut odwołania ma określoną wartość są żądania dotyczące elementów członkowskich atrybutu.  

![][5]
*Rysunek 6: Grupy aprowizację i anulowanie obsługi sekwencji*

## <a name="related-articles"></a>Pokrewne artykuły:
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Automatyzowanie użytkownika udostępniania/anulowania obsługi do aplikacji SaaS](active-directory-saas-app-provisioning.md)
* [Dostosowywanie mapowań atrybutów do inicjowania obsługi użytkowników](active-directory-saas-customizing-attribute-mappings.md)
* [Tworzenie wyrażeń na potrzeby mapowań atrybutów](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtry zakresu dla Inicjowanie obsługi użytkowników](active-directory-saas-scoping-filters.md)
* [Powiadomienia aprowizacji kont](active-directory-saas-account-provisioning-notifications.md)
* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
