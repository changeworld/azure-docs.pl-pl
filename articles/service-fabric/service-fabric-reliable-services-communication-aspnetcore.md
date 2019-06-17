---
title: Usługi komunikacji z platformą ASP.NET Core | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać platformy ASP.NET Core bezstanowych i stanowych usług Reliable Services.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939792"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Platforma ASP.NET Core usług Reliable Services usługi Azure Service Fabric

Platforma ASP.NET Core to architektura typu open source i dla wielu platform. Ta struktura jest przeznaczony do tworzenia aplikacji opartych na chmurze, podłączonej do Internetu, takich jak aplikacje sieci web, aplikacji IoT i zaplecza aplikacji mobilnych.

Ten artykuł to podręcznik szczegółowe do udostępniania usług platformy ASP.NET Core usług Reliable Services usługi Service Fabric przy użyciu **Microsoft.ServiceFabric.AspNetCore.** zestaw pakietów NuGet.

Aby uzyskać Samouczek wprowadzający programu ASP.NET Core w usłudze Service Fabric i instrukcje dotyczące konfigurowania środowiska deweloperskiego, konfigurowanie, zobacz [samouczka: Tworzenie i wdrażanie aplikacji przy użyciu usługi frontonu internetowego interfejsu API platformy ASP.NET Core i stanowej usługi zaplecza](service-fabric-tutorial-create-dotnet-app.md).

W pozostałej części tego artykułu przyjęto założenie, że już znasz platformy ASP.NET Core. Jeśli nie, przeczytaj [podstawy platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Platforma ASP.NET Core w środowisku usługi Service Fabric

Aplikacje platformy ASP.NET Core i usługi Service Fabric można uruchamiać na platformy .NET Core lub pełny program .NET Framework. Program ASP.NET Core na dwa różne sposoby, w usłudze Service Fabric:
 - **Hostowany jako wykonywalnej gościa**. W ten sposób jest używany głównie uruchamianie istniejących aplikacji programu ASP.NET Core w usłudze Service Fabric, bez konieczności wprowadzania zmian kodu.
 - **Uruchamiane wewnątrz niezawodnej usługi**. W ten sposób umożliwia lepsza integracja ze środowiskiem uruchomieniowym usługi Service Fabric i umożliwia usług stanowych platformy ASP.NET Core.

W pozostałej części tego artykułu opisano sposób używanie platformy ASP.NET Core w niezawodnej usługi, za pomocą platformy ASP.NET Core składniki integracji, które są dostarczane z zestawem SDK usługi Service Fabric.

## <a name="service-fabric-service-hosting"></a>Hosting usługi Service Fabric

W usłudze Service Fabric, jeden lub więcej wystąpień i/lub repliki usługi uruchamiane w *proces hosta usługi*: plik wykonywalny, który uruchamia kod usługi. Jako autor usługi, masz procesem hosta usługi, a Usługa Service Fabric aktywuje i monitoruje go dla Ciebie.

Tradycyjne ASP.NET (maksymalnie MVC 5) jest ściśle powiązany w usługach IIS za pomocą System.Web.dll. Platforma ASP.NET Core rozdzielenie między serwerem sieci web i aplikacji sieci web. Ta separacja pozwala aplikacjom sieci web można przenosić między różnych serwerów internetowych. Umożliwia także jako serwery sieci web *może być samodzielnie hostowane*. Oznacza to, że można uruchomić serwera sieci web w swoim własnym procesie, w przeciwieństwie do procesu, który jest własnością oprogramowanie serwerowe dedykowanej sieci web, takich jak usługi IIS.

Aby połączyć usługę Service Fabric i programu ASP.NET, jako wykonywalnej gościa lub w usługi reliable service musi być możliwe uruchomienie programu ASP.NET w procesie hosta usługi. Platforma ASP.NET Core hostingu samodzielnego umożliwia to zrobić.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostingu platformy ASP.NET Core w usłudze reliable service
Zazwyczaj własne aplikacje platformy ASP.NET Core tworzenie hostem sieci Web w punkcie wejścia aplikacji, takich jak `static void Main()` method in Class metoda `Program.cs`. W tym przypadku cyklu życia hostem sieci Web jest powiązana cyklu życia procesu.

![Hostingu platformy ASP.NET Core w procesie][0]

Ale punkt wejścia aplikacji nie jest właściwym miejscem do tworzenia hostem sieci Web w niezawodnej usługi. Wynika to z punktu wejścia aplikacji tylko służy do rejestrowania typ usługi ze środowiskiem uruchomieniowym usługi Service Fabric, dzięki czemu może utworzyć wystąpienia typu usługi. Hostem sieci Web powinny zostać utworzone w samej niezawodne usługi. W ramach procesu hosta usługi wystąpień usługi i/lub replik może przechodzą wiele cykli życia. 

Wystąpienie usługi Reliable Service jest reprezentowany przez pochodząca od klasy usługi `StatelessService` lub `StatefulService`. Stos komunikacji usługi znajduje się w `ICommunicationListener` implementacji w klasie usługi. `Microsoft.ServiceFabric.AspNetCore.*` Pakiety NuGet zawiera implementacje `ICommunicationListener` , uruchom i zarządzać hostem sieci Web platformy ASP.NET Core Kestrel lub sterownik HTTP.sys w niezawodnej usługi.

![Diagram hostingu platformy ASP.NET Core w usłudze reliable service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners platformy ASP.NET Core
`ICommunicationListener` Niedotyczące HTTP.sys w usługach Kestrel i `Microsoft.ServiceFabric.AspNetCore.*` pakiety NuGet mają podobne wzorców użycia. Ale wykonują nieco różne akcje specyficzne dla każdego serwera sieci web. 

Zarówno odbiorników komunikacji zapewniają konstruktora, który przyjmuje następujące argumenty:
 - **`ServiceContext serviceContext`** : Jest to `ServiceContext` obiektu, który zawiera informacje dotyczące uruchomionej usługi.
 - **`string endpointName`** : Jest to nazwa `Endpoint` konfiguracji w ServiceManifest.xml. To przede wszystkim których odbiorników komunikacji dwóch różnią się. Sterownik HTTP.sys *wymaga* `Endpoint` konfiguracji, a nie Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : To jest wyrażeniem lambda, które możesz wdrożyć w tworzony i zwracać `IWebHost`. Umożliwia skonfigurowanie `IWebHost` sposób zwykle w aplikacji ASP.NET Core. Wyrażenie lambda udostępnia adres URL, który jest generowany, w zależności od opcji integracji usługi Service Fabric można używać i `Endpoint` konfiguracji należy podać. Następnie można zmodyfikować lub użyj tego adresu URL, aby uruchomić serwer sieci web.

## <a name="service-fabric-integration-middleware"></a>Oprogramowania pośredniczącego integracji usługi Service Fabric
`Microsoft.ServiceFabric.AspNetCore` Obejmuje pakiet NuGet `UseServiceFabricIntegration` metody rozszerzenia `IWebHostBuilder` , dodaje oprogramowanie pośredniczące obsługującej usługi Service Fabric. To oprogramowanie pośredniczące konfiguruje Kestrel lub sterownik HTTP.sys `ICommunicationListener` zarejestrować adres URL, unikatowa usługa, za pomocą usługi nazewnictwa Service Fabric. Następnie weryfikuje żądań klientów, aby upewnić się, że łączą się odpowiednią usługę klienci. 

Ten krok jest niezbędny uniemożliwić klientom przez pomyłkę połączenia z usługą problem. To, ponieważ w środowisku hosta udostępnione takich jak usługi Service Fabric można uruchomić na sam fizyczny lub maszyna wirtualna wielu aplikacji sieci web, ale nie należy używać nazw hostów unikatowy. Ten scenariusz jest opisany bardziej szczegółowo w następnej sekcji.

### <a name="a-case-of-mistaken-identity"></a>Przypadek omyłkowe tożsamości
Repliki usługi, niezależnie od tego protokołu, nasłuchiwać IP:port unikatową kombinację. Po rozpoczęciu repliki service nasłuchiwania w punkcie końcowym IP:port wysyła raporty tego adresu punktu końcowego usługi nazewnictwa Service Fabric. Istnieje klientów lub innych usług, można je odnajdą. Użycie porty dynamiczne przypisanej aplikacji, usług repliki usługi przypadkowo może używać tego samego IP:port punkt końcowy przez inną usługę wcześniej w tym samym fizyczne lub maszyny wirtualnej. Może to spowodować, że klient nawiązać omyłkowo niewłaściwego usługi. W tym scenariuszu może spowodować, jeśli ma miejsce następująca sekwencja zdarzeń:

 1. A usługa nasłuchuje na 10.0.0.1:30000 za pośrednictwem protokołu HTTP. 
 2. Klient jest rozpoznawana jako usługa A i pobiera 10.0.0.1:30000 adresu.
 3. Usługa A przenosi się do innego węzła.
 4. Usługa B jest umieszczany na 10.0.0.1 i przypadkowo używa tego samego portu 30000.
 5. Klient próbuje nawiązać połączenie z usługi A za pomocą 10.0.0.1:30000 adres pamięci podręcznej.
 6. Klient jest teraz pomyślnie nawiązano połączenie usługi B, nie chcą korzystać połączoną usługą problem.

Może to spowodować błędy w losowych odstępach czasu, które mogą być trudne do zdiagnozowania.

### <a name="using-unique-service-urls"></a>Przy użyciu adresów URL, unikatowa usługa
Aby uniknąć tych błędów, usługi można wpis punktu końcowego w usłudze nazewnictwa z unikatowym identyfikatorem, a następnie zweryfikować tego unikatowego identyfikatora podczas żądania klienta. To wspólne działanie między usługami w zaufanym środowisku — dzierżawy szkodliwy. Nie zapewnia usługa bezpiecznego uwierzytelniania w środowisku szkodliwy dzierżawy.

W zaufanym środowisku oprogramowania pośredniczącego, które jest dodawany przez `UseServiceFabricIntegration` metoda automatycznie dołącza Unikatowy identyfikator adres, który został opublikowany w usłudze nazewnictwa. Sprawdza poprawność identyfikatora na każde żądanie. Jeśli identyfikator nie jest zgodny, oprogramowanie pośredniczące natychmiast zwraca odpowiedź HTTP 410 — Przeniesiono.

Usługi używające dynamicznie przypisany port należy wprowadzić korzystanie z tego oprogramowania pośredniczącego.

Usług korzystających z stały port unikatowy nie mają tego problemu w środowisku współpracy. Zazwyczaj służy stały port unikatowy dla zewnętrznie połączonego z usługi, które wymagają dobrze znany port dla aplikacji klienckich nawiązać połączenie. Na przykład większość aplikacji sieci web dostępnym z Internetu użyje portu 80 i 443 dla połączeń w przeglądarce sieci web. W tym przypadku Unikatowy identyfikator nie powinny być włączone.

Na poniższym diagramie przedstawiono przepływ żądania z oprogramowaniem pośredniczącym włączone:

![Integracja platformy ASP.NET Core w usłudze Service Fabric][2]

Kestrel i sterownik HTTP.sys `ICommunicationListener` implementacje używać tego mechanizmu w taki sam sposób. Mimo że sterownik HTTP.sys może wewnętrznie odróżniać zapytania oparte na unikatowych ścieżek URL przy użyciu podstawowych **HTTP.sys** funkcja, która funkcja jest udostępniania portów *nie* używany przez rozszerzenie HTTP.sys `ICommunicationListener`implementacji. To, ponieważ powoduje ona HTTP 503 i HTTP 404 kody stanu błędu w scenariuszu opisano wcześniej. Który z kolei utrudnia dla klientów w celu określenia celem tego błędu, HTTP 503 i HTTP 404 często są używane do wskazania innych błędów. 

W efekcie Kestrel i sterownik HTTP.sys `ICommunicationListener` implementacje standaryzuj udostępniane przez oprogramowanie pośredniczące `UseServiceFabricIntegration` — metoda rozszerzenia. W związku z tym klienci muszą tylko do wykonania akcji ponownie rozpoznawać punktu końcowego usługi w odpowiedzi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>Sterownik HTTP.sys usług Reliable Services
Umożliwia HTTP.sys usług Reliable Services, importując **Microsoft.ServiceFabric.AspNetCore.HttpSys** pakietu NuGet. Ten pakiet zawiera `HttpSysCommunicationListener`, implementacja `ICommunicationListener`. `HttpSysCommunicationListener` Służy do tworzenia platformy ASP.NET Core WebHost, wewnątrz usługi reliable service za pomocą HTTP.sys jako serwer sieci web.

Sterownik HTTP.sys jest oparta na [interfejsu API serwera HTTP Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Ten interfejs API nawiązuje **HTTP.sys** sterownik jądra na potrzeby przetwarzania żądań HTTP i przekazywać je do procesów, którzy uruchamiają aplikacje sieci web. Dzięki temu wiele procesów w tej samej fizyczne lub maszyny wirtualnej do hosta aplikacji sieci web na tym samym porcie rozróżniane przy albo unikatowy adres URL ścieżkę lub nazwę hosta. Funkcje te są przydatne w usłudze Service Fabric do hostowania wielu witryn sieci Web, w tym samym klastrze.

>[!NOTE]
>Implementacja HTTP.sys działa tylko na platformy Windows.

Na poniższym diagramie przedstawiono, jak korzysta z HTTP.sys **HTTP.sys** sterownik jądra na Windows współużytkowania portów:

![Sterownik HTTP.sys diagramu][3]

### <a name="httpsys-in-a-stateless-service"></a>Sterownik HTTP.sys usługi bezstanowej
Aby użyć `HttpSys` w usługę bezstanową, należy zastąpić `CreateServiceInstanceListeners` metody i wróć `HttpSysCommunicationListener` wystąpienia:

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

### <a name="httpsys-in-a-stateful-service"></a>Sterownik HTTP.sys w stanowej usłudze

`HttpSysCommunicationListener` obecnie nie jest przeznaczony dla usług stanowych ze względu na kompilacji z bazowego **HTTP.sys** funkcji udostępniania portów. Aby uzyskać więcej informacji zobacz następującą sekcję w alokacji portów dynamicznych w pliku HTTP.sys. W przypadku usług stanowych Kestrel jest serwer sieci web sugerowane.

### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

`Endpoint` Konfiguracja jest wymagana dla serwerów sieci web, korzystających z Windows API serwera HTTP, w tym HTTP.sys. Serwery sieci Web, które używają interfejsu API serwera HTTP Windows najpierw należy zarezerwować swój adres URL w pliku HTTP.sys (zwykle jest to realizowane przy [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) narzędzie). 

Ta akcja wymaga podniesionych uprawnień, które usługi nie mają domyślnie. Opcje "http" lub "https" `Protocol` właściwość `Endpoint` konfiguracji w ServiceManifest.xml służą specjalnie w celu poinstruowania uruchomieniowe usługi Service Fabric, aby zarejestrować adres URL w pliku HTTP.sys w Twoim imieniu. Jest to realizowane przy użyciu [ *silny symbol wieloznaczny* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefiks adresu URL.

Na przykład, aby zarezerwować `http://+:80` dla usługi, należy użyć następującej konfiguracji ServiceManifest.xml:

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

I nazwa punktu końcowego muszą zostać przekazane do `HttpSysCommunicationListener` Konstruktor:

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

#### <a name="use-httpsys-with-a-static-port"></a>Sterownik HTTP.sys za pomocą portu statycznego
Aby korzystanie z portu statycznego w pliku HTTP.sys, podaj numer portu w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Sterownik HTTP.sys za pomocą portu dynamicznego
Korzystanie z portu przypisywany dynamicznie w pliku HTTP.sys, Pomiń `Port` właściwość `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Port dynamiczny przydzielonej przez `Endpoint` konfiguracji zawiera tylko jeden port *na proces hosta*. Bieżący model hostingu usługi Service Fabric umożliwia wielu wystąpień usługi i/lub replik hostowane w tym samym procesie. Oznacza to, że każdy z nich współużytkują ten sam port, gdy przydzielane za pośrednictwem `Endpoint` konfiguracji. Wiele **HTTP.sys** wystąpień może współużytkować port przy użyciu podstawowych **HTTP.sys** funkcji udostępniania portów. Ale nie jest obsługiwana przez `HttpSysCommunicationListener` ze względu na komplikacje wprowadza do obsługi żądań klientów. Użycie portu dynamicznego Kestrel jest serwer sieci web sugerowane.

## <a name="kestrel-in-reliable-services"></a>Kestrel usług Reliable Services
Można użyć Kestrel usług Reliable Services, importując **Microsoft.ServiceFabric.AspNetCore.Kestrel** pakietu NuGet. Ten pakiet zawiera `KestrelCommunicationListener`, implementacja `ICommunicationListener`. `KestrelCommunicationListener` Służy do tworzenia platformy ASP.NET Core WebHost, wewnątrz usługi reliable service za pomocą Kestrel jako serwer sieci web.

Kestrel jest oparte na serwerze sieci web dla wielu platform dla platformy ASP.NET Core na libuv, bibliotekę asynchronicznych operacji We/Wy dla wielu platform. W odróżnieniu od HTTP.sys Kestrel nie używa Menedżera scentralizowane punktu końcowego. W odróżnieniu od HTTP.sys Kestrel nie obsługuje również udostępnianie portów między wiele procesów. Każde wystąpienie Kestrel należy użyć unikatowy port.

![Kestrel diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel usługi bezstanowej
Aby użyć `Kestrel` w usługę bezstanową, należy zastąpić `CreateServiceInstanceListeners` metody i wróć `KestrelCommunicationListener` wystąpienia:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel w stanowej usłudze
Aby użyć `Kestrel` w stanowej usłudze, należy zastąpić `CreateServiceReplicaListeners` metody i wróć `KestrelCommunicationListener` wystąpienia:

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

W tym przykładzie, pojedyncze wystąpienie `IReliableStateManager` został dostarczony do kontenera iniekcji zależności hostem sieci Web. To nie jest to niezbędne, ale pozwala na używanie `IReliableStateManager` i elementów Reliable Collections w swojej metody akcji kontrolera MVC.

`Endpoint` Nazwa konfiguracji jest *nie* udostępniane `KestrelCommunicationListener` w stanowej usłudze. Jest to wyjaśnione bardziej szczegółowo w następnej sekcji.

### <a name="configure-kestrel-to-use-https"></a>Konfigurowanie usługi Kestrel do używania protokołu HTTPS
Podczas włączania protokołu Kestrel HTTPS w usłudze, musisz ustawić kilka opcji nasłuchiwania. Aktualizacja `ServiceInstanceListener` używać *EndpointHttps* punktu końcowego i nasłuchiwania na konkretnym porcie (taki jak port 443). Podczas konfigurowania hosta sieci web do używania serwera sieci web Kestrel, należy skonfigurować usługi Kestrel do nasłuchiwania adresów IPv6 na wszystkich interfejsach sieciowych: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Aby uzyskać pełny przykład zapoznać się z samouczkiem, zobacz [skonfigurować Kestrel do używania protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego
`Endpoint` Konfiguracja nie jest wymagana w celu użycia Kestrel. 

Kestrel to prosta autonomiczna serwer sieci web. W odróżnieniu od HTTP.sys (lub HttpListener) nie wymaga `Endpoint` konfiguracji w ServiceManifest.xml, ponieważ nie wymaga adresu URL rejestracji przed rozpoczęciem. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel za pomocą portu statycznego
Można skonfigurować port statyczny w `Endpoint` konfiguracji ServiceManifest.xml do użytku z programem Kestrel. Mimo że to nie jest to niezbędne, oferuje dwie potencjalne korzyści:
 - Jeśli port nie należy do zakresu portów aplikacji, jest otwarty przez zaporę systemu operacyjnego przez usługę Service Fabric.
 - Adres URL podany dla użytkownika za pośrednictwem `KestrelCommunicationListener` użyje tego portu.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Jeśli `Endpoint` jest skonfigurowany, nazwy muszą być przekazywane do `KestrelCommunicationListener` Konstruktor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Jeśli nie używasz ServiceManifest.xml `Endpoint` konfiguracji, Pomiń nazwę w `KestrelCommunicationListener` konstruktora. W takim przypadku zostanie użyty port dynamiczny. Zobacz następną sekcję, aby uzyskać więcej informacji na ten temat.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Kestrel za pomocą portu dynamicznego
Kestrel nie można użyć przypisania portów automatycznego z `Endpoint` konfiguracji w ServiceManifest.xml. To dlatego, że automatyczne przypisanie z portu `Endpoint` konfiguracji przypisuje unikatowy port na *proces hosta*, i procesem jednego hosta może zawierać wiele wystąpień Kestrel. To nie działa z Kestrel, ponieważ program nie obsługuje udostępniania portów. W związku z tym należy otworzyć każde wystąpienie Kestrel unikatowy port.

Dynamiczne przypisywanie portów za pomocą Kestrel, Pomiń `Endpoint` konfiguracji w ServiceManifest.xml całkowicie, a nie przekazuj nazwy punktu końcowego do `KestrelCommunicationListener` konstruktora, w następujący sposób:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

W tej konfiguracji `KestrelCommunicationListener` będzie automatycznie wybierać nieużywanego portu z zakresu portów aplikacji.

## <a name="service-fabric-configuration-provider"></a>Dostawca konfiguracji usługi Service Fabric
Konfiguracja aplikacji w programie ASP.NET Core jest oparty na pary klucz wartość ustanowione przez dostawcę konfiguracji. Odczyt [konfiguracji w programie ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) Aby dowiedzieć się więcej obsługę konfiguracji platformy ASP.NET Core ogólne włączone.

W tej sekcji opisano, jak dostawca konfiguracji usługi Service Fabric, integruje się z konfiguracji platformy ASP.NET Core, importując `Microsoft.ServiceFabric.AspNetCore.Configuration` pakietu NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration uruchamiania rozszerzenia
Po zaimportowaniu `Microsoft.ServiceFabric.AspNetCore.Configuration` pakietu NuGet, musisz zarejestrować źródła konfiguracji usługi Service Fabric za pomocą platformy ASP.NET Core, konfiguracji interfejsu API. Możesz to zrobić, sprawdzając **AddServiceFabricConfiguration** rozszerzeń w `Microsoft.ServiceFabric.AspNetCore.Configuration` przestrzeni nazw przed `IConfigurationBuilder`.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Teraz usługi ASP.NET Core można uzyskać dostęp do ustawień konfiguracji usługi Service Fabric, podobnie jak inne ustawienia aplikacji. Na przykład umożliwia wzorzec opcje ładowania ustawień silnie typizowanych obiektów.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Domyślne mapowanie klawiszy
Domyślnie dostawca konfiguracji usługi Service Fabric zawiera nazwę pakietu, nazwy sekcji i nazwy właściwości. Razem tworzą one klucz konfiguracji platformy ASP.NET Core w następujący sposób:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Na przykład, jeśli masz pakiet konfiguracji o nazwie `MyConfigPackage` o następującej zawartości, następnie wartość konfiguracji będzie dostępny dla platformy ASP.NET Core `IConfiguration` za pośrednictwem *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opcje konfiguracji usługi Service Fabric
Dostawca konfiguracji usługi Service Fabric obsługuje także `ServiceFabricConfigurationOptions` Aby zmienić zachowanie domyślne mapowania klucza.

#### <a name="encrypted-settings"></a>Ustawienia szyfrowania
Usługa Service Fabric obsługuje ustawienia szyfrowania, jak dostawca konfiguracji usługi Service Fabric. Ustawienia szyfrowania nie są odszyfrowywane do platformy ASP.NET Core `IConfiguration` domyślnie. Zaszyfrowane wartości są przechowywane tam zamiast tego. Ale jeśli chcesz można odszyfrować wartości do przechowywania w wartości IConfiguration programu ASP.NET Core, można ustawić *DecryptValue* flagi na wartość false w `AddServiceFabricConfiguration` rozszerzenie w następujący sposób:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Wiele konfiguracji pakietów
Usługa Service Fabric obsługuje wiele konfiguracji pakietów. Domyślnie nazwa pakietu znajduje się w kluczu konfiguracji. Ale można ustawić `IncludePackageName` flagi na wartość false, w następujący sposób:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Niestandardowe mapowanie klawiszy, wyodrębnianie wartości i wypełnianie danymi
Dostawca konfiguracji usługi Service Fabric obsługuje również bardziej zaawansowane scenariusze umożliwiające dostosowywanie mapowania klucza przy użyciu `ExtractKeyFunc` i niestandardowe — wyodrębnianie wartości z `ExtractValueFunc`. Można nawet zmienić cały proces wypełnianie danych z usługi Service Fabric konfiguracji do konfiguracji platformy ASP.NET Core przy użyciu `ConfigAction`.

Poniższe przykłady ilustrują, jak używać `ConfigAction` dostosować wypełnianie danymi:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Aktualizacje konfiguracji
Dostawca konfiguracji usługi Service Fabric obsługuje także aktualizacje konfiguracji. Możesz użyć platformy ASP.NET Core `IOptionsMonitor` otrzymywać powiadomienia o zmianach, a następnie użyć `IOptionsSnapshot` ponowne załadowanie danych konfiguracji. Aby uzyskać więcej informacji, zobacz [opcji platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Te opcje są obsługiwane domyślnie. Do włączenia aktualizacji konfiguracji jest wymagane żadne dodatkowe kodowania.

## <a name="scenarios-and-configurations"></a>Scenariusze i konfiguracji
Ta sekcja zawiera kombinację serwera sieci web, Konfiguracja portu, opcje integracji usługi Service Fabric i różne ustawienia, które firma Microsoft zaleca rozwiązywać problemy z następujących scenariuszy:
 - Udostępniane zewnętrznie bezstanowej usługi ASP.NET Core
 - Tylko do wewnętrznych usług bezstanowych platformy ASP.NET Core
 - Tylko do wewnętrznych usług stanowych platformy ASP.NET Core

**Udostępniane zewnętrznie usługi** to taki, który uwidacznia punkt końcowy, który jest wywoływany z poza klastrem, zwykle za pośrednictwem modułu równoważenia obciążenia.

**Wyłącznie wewnętrznym** usługi jest jednym którego następuje wywołanie punktu końcowego tylko z w ramach klastra.

> [!NOTE]
> Punkty końcowe usługi stanowej ogólnie nie powinny być połączone z Internetem. Klastry za modułem równoważenia obciążenia, które znają rozwiązania usługi Service Fabric, takich jak usługi Azure Load Balancer można uwidocznić usług stanowych. Wynika to z modułu równoważenia obciążenia nie można zlokalizować i kierowania ruchu do replik odpowiednie usługi stanowej. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Udostępniane zewnętrznie bezstanowej usługi ASP.NET Core
Kestrel jest serwer sieci web sugerowanych dla usług frontonu, które uwidaczniają punkty końcowe HTTP zewnętrznych, połączone z Internetem. W Windows sterownik HTTP.sys może zapewnić możliwość udostępniania portów, dzięki czemu można hostować wiele usług sieci web na tym samym zestawie węzłów przy użyciu tego samego portu. W tym scenariuszu usługi sieci web są zróżnicowane według nazwy hosta lub ścieżki, bez konieczności polegania na frontonu serwera proxy lub gateway zapewniają routing protokołu HTTP.
 
Gdy połączenie z Internetem, usługa bezstanowa należy używać dobrze znanych i stabilnych punktu końcowego, który jest dostępny za pośrednictwem modułu równoważenia obciążenia. Ten adres URL udostępni użytkownikom Twojej aplikacji. Zalecamy następującą konfigurację:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Kestrel jest serwer preferowanych sieci web, ponieważ jest on obsługiwany w Windows i Linux. |
| Konfiguracja portu | Statyczne | Dobrze znanego portu statycznego powinna być skonfigurowana w `Endpoints` konfiguracji ServiceManifest.xml, takie jak 80 dla protokołu HTTP i 443 dla protokołu HTTPS. |
| ServiceFabricIntegrationOptions | Brak | Użyj `ServiceFabricIntegrationOptions.None` opcję podczas konfigurowania oprogramowania pośredniczącego integracji usługi Service Fabric, tak aby usługi nie próbuje sprawdzić żądań przychodzących dla Unikatowy identyfikator. Użytkownicy zewnętrzni aplikacji nie będzie wiedzieć, unikatowe informacje identyfikujące, która używa oprogramowania pośredniczącego. |
| Liczba wystąpień | -1 | Typowe przypadki użycia, liczba wystąpień, ustawienie powinny zostać ustawione *-1*. Można to zrobić, aby wystąpienie jest dostępna we wszystkich węzłach, odbierające ruch z modułu równoważenia obciążenia. |

Jeśli wiele zewnętrznie uwidocznionych usług mają ten sam zestaw węzłów, używając HTTP.sys ze ścieżką URL unikatowy, ale stabilne. Można to zrobić po zmodyfikowaniu adresu URL, podane podczas konfigurowania IWebHost. Należy zauważyć, że dotyczy to sterownik HTTP.sys tylko.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Tylko do wewnętrznego bezstanowa usługa ASP.NET Core
Bezstanowej usługi, które są wywołana tylko z w ramach klastra należy używać unikatowe adresy URL i dynamicznie przypisywany portów w celu zapewnienie współpracy między wiele usług. Zalecamy następującą konfigurację:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Mimo że sterownik HTTP.sys można użyć w przypadku usług bezstanowych wewnętrznego, Kestrel jest najlepsze serwera, aby umożliwić wiele wystąpień usługi udostępnić hosta.  |
| Konfiguracja portu | dynamicznie przypisany | Wiele replik stanowej usługi może współużytkować procesu hosta lub systemu operacyjnego hosta, a więc musi unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Za pomocą dynamiczne przypisywanie portów to ustawienie zapobiega opisanego wcześniej problemu omyłkowe tożsamości. |
| InstanceCount | Wszystkie | Liczba wystąpień ustawienia można ustawić dowolną wartość niezbędne w celu oferowania usługi. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Tylko do wewnętrznego stanowej usługi ASP.NET Core
Usługi stanowe, które są wywołana tylko z w ramach klastra należy używać portów przypisywany dynamicznie zapewnienie współpracy między wiele usług. Zalecamy następującą konfigurację:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | `HttpSysCommunicationListener` Nie jest przeznaczony do użytku przez usługi stanowe, w których replik udostępnianie procesu hosta. |
| Konfiguracja portu | dynamicznie przypisany | Wiele replik stanowej usługi może współużytkować procesu hosta lub systemu operacyjnego hosta, a więc musi unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Za pomocą dynamiczne przypisywanie portów to ustawienie zapobiega opisanego wcześniej problemu omyłkowe tożsamości. |

## <a name="next-steps"></a>Kolejne kroki
[Debug your Service Fabric application by using Visual Studio (Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio)](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
