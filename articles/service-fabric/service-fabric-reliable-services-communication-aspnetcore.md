---
title: Usługa Komunikacja z platformy ASP.NET Core | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z platformy ASP.NET Core w bezstanowe i stanowe niezawodne usługi.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 7786e08e04d2ebce757b4c47b8ed599036c95958
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207863"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Platformy ASP.NET Core w niezawodnej usługi sieci szkieletowej usług

Platformy ASP.NET Core to nowa open source i międzyplatformowa struktura do tworzenia nowoczesnych aplikacji działających w chmurze podłączonej do Internetu, takich jak aplikacje sieci web, aplikacji IoT i przenośnych zapleczy. 

Ten artykuł zawiera szczegółowy przewodnik z usługami platformy ASP.NET Core za pomocą usługi sieć szkieletowa niezawodne usługi hostingu **Microsoft.ServiceFabric.AspNetCore.** * zestaw pakietów NuGet.

Samouczek wprowadzający platformy ASP.NET Core w sieci szkieletowej usług i instrukcje dotyczące konfigurowania środowiska deweloperskiego, konfigurowanie, zobacz [utworzyć aplikację .NET](service-fabric-tutorial-create-dotnet-app.md).

Pozostała część w tym artykule przyjęto założenie, że już znasz platformy ASP.NET Core. Jeśli nie, firma Microsoft zaleca odczytu za pomocą [podstawowe informacje na temat platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Platformy ASP.NET Core w środowisku sieci szkieletowej usług

Gdy aplikacje platformy ASP.NET Core można uruchomić na .NET Core lub pełną .NET Framework, usług sieci szkieletowej usług obecnie można uruchamiać tylko na pełne .NET Framework. Oznacza to, podczas tworzenia usługi platformy ASP.NET Core Service Fabric, nadal muszą wskazywać z pełnego środowiska .NET Framework.

Platformy ASP.NET Core mogą być używane w sieci szkieletowej usług na dwa sposoby:
 - **Hostowany jako pliku wykonywalnego gościa**. Służy to głównie do uruchomienia aplikacji platformy ASP.NET Core w sieci szkieletowej usług bez zmian kodu.
 - **Uruchom wewnątrz niezawodnej usługi**. Umożliwia lepszą integrację z środowiska uruchomieniowego platformy Service Fabric i umożliwia stanowe platformy ASP.NET Core services.

Pozostałe w tym artykule opisano sposób korzystania z platformy ASP.NET Core wewnątrz niezawodnej usługi za pomocą platformy ASP.NET Core składniki integracji, które są dostarczane przy użyciu zestawu SDK sieci szkieletowej usług. 

## <a name="service-fabric-service-hosting"></a>Hosting usług sieci szkieletowej usług

W sieci szkieletowej usług, jeden lub więcej wystąpień i/lub repliki usługi uruchamiane w *procesu hosta usługi*, plik wykonywalny, który uruchamia kodu usługi. Jako autor usługi, własnej procesu hosta usługi i usługi Service Fabric aktywuje i monitoruje go dla Ciebie.

Tradycyjny ASP.NET (maksymalnie MVC 5) jest ściśle powiązane do usług IIS za pomocą System.Web.dll. Platformy ASP.NET Core rozdzielenie między serwerem sieci web i aplikacji sieci web. Umożliwia aplikacji sieci web jako przenośny między serwerami innej witryny sieci web i umożliwia także serwery sieci web jako *hosta samodzielnego*, co oznacza, że można uruchomić serwera sieci web własne procesu, w przeciwieństwie do procesu, który jest właścicielem oprogramowanie serwera sieci web, takich jak usługi IIS w wersji dedykowanej. 

Aby można było połączyć usługi Service Fabric i ASP.NET, jako pliku wykonywalnego gościa lub w niezawodnej usługi, musi być możliwe jej uruchomienie ASP.NET wewnątrz procesu hosta usługi. Platformy ASP.NET Core hostingu samodzielnego można to zrobić.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting platformy ASP.NET Core w niezawodnej usługi
Zazwyczaj własnym obsługiwanych aplikacji platformy ASP.NET Core tworzenie WebHost w punkcie wejścia aplikacji, takich jak `static void Main()` metoda `Program.cs`. W takim przypadku cyklem życia hostem sieci Web jest powiązany z cyklem życia procesu.

![Hosting platformy ASP.NET Core w procesie][0]

Jednak punkt wejścia aplikacji nie jest odpowiednim miejscu, aby utworzyć WebHost w niezawodnej usługi, ponieważ punkt wejścia aplikacji tylko służy do rejestrowania typ usługi ze środowiskiem uruchomieniowym usługi sieć szkieletowa, dzięki czemu może utworzyć wystąpienia typu usługi. Hostem sieci Web należy utworzyć w niezawodnej usługi samej siebie. W ramach procesu hosta usługi wystąpień usługi i/lub repliki można przejść przez wiele cyklów. 

Wystąpienie usługi niezawodnego jest reprezentowana przez usługi klasy wywodzące się z `StatelessService` lub `StatefulService`. Stosu komunikacji usługi znajduje się w `ICommunicationListener` wdrażania w klasie usługi. `Microsoft.ServiceFabric.Services.AspNetCore.*` Pakietów NuGet zawiera implementacje `ICommunicationListener` czy start i zarządzać hostem sieci Web platformy ASP.NET Core dla Kestrel lub HttpSys w niezawodnej usługi.

![Hosting platformy ASP.NET Core w niezawodnej usługi][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners platformy ASP.NET Core
`ICommunicationListener` Implementacje Kestrel i HttpSys w `Microsoft.ServiceFabric.Services.AspNetCore.*` mają podobne wzorce użycia pakietów NuGet, ale akcje nieco inne specyficzne dla każdego serwera sieci web. 

Zarówno odbiorników komunikacji zawierają konstruktora, który ma następujące argumenty:
 - **`ServiceContext serviceContext`**: `ServiceContext` Obiektu, który zawiera informacje dotyczące uruchomionej usługi.
 - **`string endpointName`**: nazwa `Endpoint` konfiguracji w pliku ServiceManifest.xml. To przede wszystkim których różnią się odbiorników komunikacji dwóch: HttpSys **wymaga** `Endpoint` konfiguracji, a nie Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: lambda, który implementuje, w którym można utworzyć i zwracany `IWebHost`. Dzięki temu można skonfigurować `IWebHost` sposób zwykle w aplikacji platformy ASP.NET Core. Wyrażenie lambda zawiera Użyj adresu URL, który jest generowany, można w zależności od sieci szkieletowej usług integracji opcjach dostępnych i `Endpoint` konfiguracji należy podać. Adres URL następnie można zmodyfikować lub używane jako — jest uruchomienie serwera sieci web.

## <a name="service-fabric-integration-middleware"></a>Oprogramowanie pośredniczące integracji sieci szkieletowej usług
`Microsoft.ServiceFabric.Services.AspNetCore` Zawiera pakiet NuGet `UseServiceFabricIntegration` — metoda rozszerzenia na `IWebHostBuilder` dodaje oprogramowanie pośredniczące obsługujący usługi sieci szkieletowej. To oprogramowanie pośredniczące konfiguruje Kestrel lub HttpSys `ICommunicationListener` można zarejestrować adresu URL usługi unikatowy usługi nazewnictwa sieci szkieletowej i sprawdza poprawność żądań klientów, aby upewnić się, klienci są połączenie z usługą prawo. Jest to konieczne w środowisku udostępnionych hosta, na przykład sieci szkieletowej usług, gdy wiele aplikacji sieci web można uruchamiać na takie same fizyczne lub maszyny wirtualnej, ale nie należy używać nazwy hosta unikatowy, aby uniemożliwić klientom przez pomyłkę połączenie z usługą niewłaściwy. Ten scenariusz jest opisany bardziej szczegółowo w następnej sekcji.

### <a name="a-case-of-mistaken-identity"></a>Przypadek omyłkowo wystąpiła tożsamości
Repliki usługi, niezależnie od protokołu, nasłuchiwania IP:port unikatowych kombinacji. Po repliki usługi rozpoczął nasłuchiwanie na punkt końcowy IP:port, zgłasza go adres tego punktu końcowego usługi nazewnictwa sieci szkieletowej usług gdzie mogły być odnajdowane przez klientów lub innych usług. Użycie portów przypisywane dynamicznie aplikacji, usług repliki usługi przypadkowo może używać tego samego punktu końcowego IP:port innej usługi, która była wcześniej w tej samej fizycznej lub maszyny wirtualnej. Może to spowodować klientowi mistakely połączyć się z usługą niewłaściwy. Może się to zdarzyć, jeśli występuje następująca sekwencja zdarzeń:

 1. Usługa A nasłuchuje 10.0.0.1:30000 za pośrednictwem protokołu HTTP. 
 2. Klient rozpoznaje Service A i pobiera adres 10.0.0.1:30000
 3. Usługa A są przenoszone do innego węzła.
 4. B usługi jest umieszczona na 10.0.0.1 i przypadkowo używa tego samego portu 30000.
 5. Klient próbuje nawiązać A service z 10.0.0.1:30000 adres pamięci podręcznej.
 6. Klient jest teraz pomyślnie nawiązano połączenie usługi B wiedzy nie jest połączona z niewłaściwej usługi.

Może to powodować błędy w losowych odstępach czasu, które mogą być trudne do diagnozowania. 

### <a name="using-unique-service-urls"></a>Przy użyciu usługi unikatowe adresy URL
Aby tego uniknąć, usługi mogą ogłoszeniem punkt końcowy Naming Service z unikatowym identyfikatorem, a następnie zweryfikować Unikatowy identyfikator podczas żądania klienta. To jest akcją współpracy między usługami w zaufanym środowisku — dzierżawy szkodliwy. Zapewnia to usługa bezpiecznego uwierzytelniania w środowisku szkodliwy dzierżawy.

W zaufanym środowisku, który jest dodawany przez oprogramowanie pośredniczące `UseServiceFabricIntegration` metody automatycznie dołącza Unikatowy identyfikator adres, który jest przesyłana z usługą Naming i sprawdza poprawność identyfikator dla każdego żądania. Jeśli identyfikator nie jest zgodny, oprogramowanie pośredniczące natychmiast zwraca odpowiedź HTTP 410 — Przeniesiono.

Usługi używające należy się upewnić, port przypisywane dynamicznie używać tego oprogramowania pośredniczącego.

W środowisku współpracy usług korzystających z stały port unikatowy nie mają ten problem. Stały port unikatowy jest zwykle używany dla zewnętrznie połączonej usługi, które wymagają dobrze znanego portu dla aplikacji klienckich nawiązać połączenie. Na przykład większość aplikacji sieci web skierowane do Internetu będzie używać portu 80 i 443 dla połączeń przeglądarki sieci web. W takim przypadku Unikatowy identyfikator nie powinna być włączona.

Na poniższym diagramie przedstawiono przepływ żądania z oprogramowaniem pośredniczącym włączone:

![Integracja usługi sieci szkieletowej platformy ASP.NET Core][2]

Zarówno Kestrel i HttpSys `ICommunicationListener` implementacje Użyj ten mechanizm w taki sam sposób. Mimo że HttpSys wewnętrznie pozwala odróżnić żądań oparte na unikatowych ścieżki adresu URL za pomocą podstawowych *http.sys* funkcji, które są funkcji współużytkowania portów *nie* używane przez HttpSys `ICommunicationListener` Implementacja ponieważ skutkiem będzie HTTP 503 i HTTP 404 kodów stanu błędu w tym scenariuszu opisano wcześniej. Które z kolei utrudnia bardzo dla klientów w celu określenia celem tego błędu, jak HTTP 503 i 404 protokołu HTTP są już powszechnie używana do określenia inne błędy. W związku z tym zarówno Kestrel i HttpSys `ICommunicationListener` implementacje normalizacji na udostępniane przez oprogramowanie pośredniczące `UseServiceFabricIntegration` — metoda rozszerzenia, dzięki czemu klienci należy wykonywać tylko punkt końcowy usługi ponownego rozpoznania akcji na odpowiedzi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys w niezawodne usługi
HttpSys mogą być używane w niezawodnej usługi przez importowanie **Microsoft.ServiceFabric.AspNetCore.HttpSys** pakietu NuGet. Ten pakiet zawiera `HttpSysCommunicationListener`, implementacja `ICommunicationListener`, który służy do tworzenia WebHost Core ASP.NET wewnątrz niezawodnej usługi za pomocą HttpSys jako serwera sieci web.

W oparciu HttpSys [interfejsu API serwera HTTP systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Ta metoda korzysta *http.sys* sterownik jądra używany przez usługi IIS do przetwarzania żądań HTTP i kierowania ich do procesów uruchomionych aplikacji sieci web. Dzięki temu wiele procesów w tej samej fizycznej lub maszyny wirtualnej do hosta aplikacji sieci web w tym samym porcie rozróżniane unikatowej ścieżki adresu URL lub nazwa hosta. Funkcje te są przydatne w sieci szkieletowej usług do obsługi wielu witryn sieci Web, w tym samym klastrze.

Na poniższym diagramie przedstawiono sposób używania HttpSys *http.sys* sterownik jądra w systemie Windows Udostępnianie portów:

![Sterownik HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys usługi bezstanowej
Do użycia `HttpSys` za pośrednictwem usługi bezstanowej, Zastąp `CreateServiceInstanceListeners` — metoda i przywracać `HttpSysCommunicationListener` wystąpienie:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HttpSys w usługi stanowej

`HttpSysCommunicationListener` obecnie nie jest przeznaczony dla stanowych usług z powodu komplikacji z podstawową *http.sys* funkcji współużytkowania portów. Aby uzyskać więcej informacji zobacz następującą sekcję na alokacją portów dynamicznych z HttpSys. Dla stanowych usług Kestrel jest serwer sieci web zalecane.

### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

`Endpoint` Konfiguracja jest wymagana dla serwerów sieci web, które używają API serwera HTTP systemu Windows, w tym HttpSys. Serwery sieci Web, które korzystają z interfejsu API serwera HTTP systemu Windows musi najpierw zarezerwować swojego adresu URL z *http.sys* (zwykle jest to realizowane przy użyciu [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) narzędzia). Ta akcja wymaga podniesione uprawnienia, które nie mają domyślnie usług. Opcje "http" lub "https" `Protocol` właściwość `Endpoint` konfiguracji w *ServiceManifest.xml* są używane w szczególności w celu poinstruowania środowiska uruchomieniowego platformy Service Fabric można zarejestrować adresu URL za *http.sys* na przy użyciu imieniu [ *silne symbolu wieloznacznego* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefiksu adresu URL.

Na przykład, aby zarezerwować `http://+:80` w przypadku usługi, należy użyć następującej konfiguracji w pliku ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

I nazwa punktu końcowego muszą być przekazywane do `HttpSysCommunicationListener` konstruktora:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>HttpSys za pomocą portu statycznego
Aby używać portu statycznego z HttpSys, podaj numer portu w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Użyj HttpSys z portów dynamicznych
Aby używać portu przypisywany dynamicznie z HttpSys, Pomiń `Port` właściwości w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Należy pamiętać, że port dynamiczny przydzielonej przez `Endpoint` konfiguracji zawiera tylko jeden port *na proces hosta*. Bieżący model hostingu sieci szkieletowej usług umożliwia wielu wystąpień usługi i/lub replik, które ma być obsługiwana w tym samym procesie, co oznacza każdego z nich udostępni tego samego portu, gdy przydzielane za pośrednictwem `Endpoint` konfiguracji. Wiele wystąpień HttpSys można współużytkować port przy użyciu odpowiadającego *http.sys* portu udostępniania funkcji, ale nie jest obsługiwana przez `HttpSysCommunicationListener` z powodu komplikacji wprowadza się ona do obsługi żądań klientów. Użycia portów dynamicznych Kestrel jest serwer sieci web zalecane.

## <a name="kestrel-in-reliable-services"></a>Kestrel w niezawodne usługi
Kestrel mogą być używane w niezawodnej usługi przez importowanie **Microsoft.ServiceFabric.AspNetCore.Kestrel** pakietu NuGet. Ten pakiet zawiera `KestrelCommunicationListener`, implementacja `ICommunicationListener`, który służy do tworzenia WebHost Core ASP.NET wewnątrz niezawodnej usługi za pomocą Kestrel jako serwera sieci web.

Kestrel to serwer sieci web i platform dla platformy ASP.NET Core oparta na libuv, biblioteki i platform asynchroniczne We/Wy. W odróżnieniu od HttpSys, Kestrel nie używa Menedżera scentralizowane punktu końcowego takich jak *http.sys*. I w przeciwieństwie do HttpSys, Kestrel nie obsługuje udostępniania portów między wiele procesów. Każde wystąpienie Kestrel musi używać portu unikatowy.

![kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel usługi bezstanowej
Do użycia `Kestrel` za pośrednictwem usługi bezstanowej, Zastąp `CreateServiceInstanceListeners` — metoda i przywracać `KestrelCommunicationListener` wystąpienie:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel w usługi stanowej
Aby użyć `Kestrel` w usługi stanowej, Zastąp `CreateServiceReplicaListeners` — metoda i wróć `KestrelCommunicationListener` wystąpienie:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

W tym przykładzie pojedyncze wystąpienie `IReliableStateManager` został dostarczony do kontenera iniekcji zależności hosta sieci Web. To nie jest to niezbędne, ale pozwala używać `IReliableStateManager` i niezawodne kolekcji w metody akcji kontrolera MVC.

Należy pamiętać, że `Endpoint` Nazwa konfiguracji jest **nie** dostarczony do `KestrelCommunicationListener` w usługi stanowej. Jest to wyjaśnić bardziej szczegółowo w następnej sekcji.

### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego
`Endpoint` Do użycia Kestrel nie jest wymagana konfiguracja. 

Kestrel jest prosty autonomiczny serwer sieci web; w odróżnieniu od HttpSys (lub HttpListener), nie musi `Endpoint` konfiguracji w *ServiceManifest.xml* , ponieważ nie wymaga adresu URL rejestracji przed rozpoczęciem. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel za pomocą portu statycznego
Można skonfigurować port statyczny w `Endpoint` konfiguracji ServiceManifest.xml do użytku z Kestrel. Chociaż nie jest to niezbędne, zawiera dwa potencjalnych korzyści:
 1. Jeśli port nie należy do zakresu portów aplikacji, jest otwarty przez zaporę systemu operacyjnego przez sieć szkieletowa usług.
 2. Adres URL udostępnionych za pośrednictwem `KestrelCommunicationListener` użyje tego portu.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Jeśli `Endpoint` jest skonfigurowany, jego nazwa muszą być przekazywane do `KestrelCommunicationListener` konstruktora: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Jeśli `Endpoint` konfiguracji nie jest używany, pominąć nazwę w `KestrelCommunicationListener` konstruktora. W takim przypadku będzie używany port dynamiczny. Zobacz następną sekcję, aby uzyskać więcej informacji.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Użyj Kestrel z portów dynamicznych
Kestrel nie można użyć przypisania automatyczne portu z `Endpoint` konfiguracji w pliku ServiceManifest.xml, ponieważ port automatyczne przypisanie z `Endpoint` konfiguracji przypisuje unikatowy portu na *proces hosta*, i procesem jednego hosta może zawierać wiele wystąpień Kestrel. Ponieważ Kestrel nie obsługuje udostępniania portów, to nie działa jako każde wystąpienie Kestrel muszą być otwarte na porcie unikatowy.

Dynamiczne przypisywanie portów za pomocą Kestrel, po prostu pominąć `Endpoint` konfiguracji w pliku ServiceManifest.xml całkowicie i nie są przekazywane Nazwa punktu końcowego do `KestrelCommunicationListener` konstruktora:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

W tej konfiguracji `KestrelCommunicationListener` będzie automatycznie wybierać nieużywanego portu z zakresu portów aplikacji.

## <a name="scenarios-and-configurations"></a>Scenariusze i konfiguracje
W tej sekcji opisano w następujących scenariuszach i zawiera zalecane kombinację serwera sieci web, konfiguracji portów sieci szkieletowej usług integracji opcje i różne ustawienia do osiągnięcia usługi działa prawidłowo:
 - Zewnętrznie udostępnione usługi bezstanowej platformy ASP.NET Core
 - Tylko wewnętrznie usługi bezstanowej platformy ASP.NET Core
 - Tylko wewnętrznie usługi stanowej platformy ASP.NET Core

**Zewnętrznie udostępnione** usługi jest taki, który udostępnia punktu końcowego, które są dostępne z poza klastrem, zwykle za pośrednictwem usługi równoważenia obciążenia.

**Wyłącznie wewnętrznym** usługi jest jednym których punkt końcowy tylko jest dostępny w ramach klastra.

> [!NOTE]
> Punkty końcowe usługi stanowej zwykle nie należy uwidaczniać jej w Internecie. Klastry, które są za usługi równoważenia obciążenia, które znają rozpoznawania usługi sieć szkieletowa usług, takich jak równoważenia obciążenia Azure można do udostępnienia usług stanowych, ponieważ nie będzie mogła zlokalizować i kierować ruchem do repliki odpowiednie usługi stanowej Usługa równoważenia obciążenia. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Zewnętrznie udostępnione usług bezstanowych platformy ASP.NET Core
Kestrel jest serwerem sieci web zalecane dla usług frontonu, które udostępniają zewnętrznych, internetowy punktów końcowych HTTP. W systemie Windows HttpSys może służyć do zapewnienia możliwości udostępniania portów, dzięki czemu można hostować wiele usług sieci web na tym samym zestawie węzłów za pomocą tego samego portu zróżnicowanych według nazwy hosta i ścieżkę, bez polegania na zapewnienie routingu HTTP frontonu serwera proxy lub bramy.
 
Jeśli połączenie z Internetem, usługi bezstanowej należy używać punktu końcowego dobrze znanych i stabilny, który jest dostępny za pośrednictwem usługi równoważenia obciążenia. Jest to adres URL zostanie zapewnione użytkownikom aplikacji. Zaleca się użycie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Kestrel jest serwer preferowanych sieci web, ponieważ jest ona obsługiwana przez system Windows i Linux. |
| Konfiguracja portów | statyczny | Dobrze znanego portu statycznego powinna być skonfigurowana w `Endpoints` konfiguracji ServiceManifest.xml, takie jak 80 dla protokołu HTTP i 443 dla protokołu HTTPS. |
| ServiceFabricIntegrationOptions | Brak | `ServiceFabricIntegrationOptions.None` Opcja powinna być używana podczas konfigurowania sieci szkieletowej usług integracji w oprogramowaniu pośredniczącym, aby usługa próbuje zweryfikować przychodzącego żądania Unikatowy identyfikator. Użytkownicy zewnętrzni aplikacji nie będzie wiedzieć, unikatowe informacje identyfikacyjne używane przez oprogramowanie pośredniczące. |
| Liczba wystąpień | -1 | W typowych przypadkach liczba wystąpień ustawienie powinna być równa "-1", aby wystąpienie jest dostępna we wszystkich węzłach, które odbierać dane z usługi równoważenia obciążenia. |

Jeśli wiele usług zewnętrznie narażonych współużytkować ten sam zestaw węzłów, można HttpSys ze ścieżką URL unikalny, ale stabilna. Można to zrobić, modyfikując adres URL podany podczas konfigurowania IWebHost. Należy zauważyć, że tylko dotyczy to HttpSys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Tylko wewnętrznie usługi bezstanowej platformy ASP.NET Core
Usługi bezstanowej, wywoływane tylko z klastra należy używać unikatowe adresy URL i dynamicznie przypisywane porty zapewnienie współpracy między wieloma usługami. Zaleca się użycie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Mimo że HttpSys może służyć do wewnętrznych usług bezstanowych, Kestrel jest zalecane serwera, aby umożliwić wiele wystąpień usługi udostępnić hosta.  |
| Konfiguracja portów | przypisywane dynamicznie | Wiele replik usługi stanowej może udostępnić procesu hosta lub systemu operacyjnego hosta i w związku z tym należy unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Z dynamiczne przypisywanie portów to ustawienie zapobiega problem tożsamości błędny opisanych wcześniej. |
| Wartość InstanceCount | dowolne | Liczba wystąpień ustawienie można ustawić dowolną wartość niezbędne do działania usługi. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Tylko wewnętrznie usługi stanowej platformy ASP.NET Core
Stanowe usług, które są wywoływać tylko z wewnątrz klastra należy używać portów przypisywany dynamicznie zapewnienie współpracy między wieloma usługami. Zaleca się użycie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | `HttpSysCommunicationListener` Nie jest przeznaczony do użytku przez usługi stanowej w których replik udziału procesu hosta. |
| Konfiguracja portów | przypisywane dynamicznie | Wiele replik usługi stanowej może udostępnić procesu hosta lub systemu operacyjnego hosta i w związku z tym należy unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Z dynamiczne przypisywanie portów to ustawienie zapobiega problem tożsamości błędny opisanych wcześniej. |

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie aplikacji sieci szkieletowej usług za pomocą programu Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
