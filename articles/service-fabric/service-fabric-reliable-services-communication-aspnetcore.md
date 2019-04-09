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
ms.openlocfilehash: 5a4b7514005da9e9a998dba014fa0ea6c014397a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268521"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Platforma ASP.NET Core usług usługi Service Fabric Reliable Services

ASP.NET Core to nowe środowisko typu open source i dla wielu platform do tworzenia nowoczesnych aplikacji działających w chmurze podłączonej do Internetu, takich jak aplikacje sieci web, aplikacji IoT i zapleczy aplikacji mobilnych. 

Ten artykuł to podręcznik szczegółowe do udostępniania usług platformy ASP.NET Core przy użyciu usług Reliable Services usługi Service Fabric **Microsoft.ServiceFabric.AspNetCore.** * zestaw pakietów NuGet.

Aby uzyskać Samouczek wprowadzający programu ASP.NET Core w usłudze Service Fabric i instrukcje dotyczące konfigurowania ustawień środowiska programowania, zobacz [tworzenie aplikacji .NET](service-fabric-tutorial-create-dotnet-app.md).

W pozostałej części tego artykułu przyjęto założenie, że już znasz platformy ASP.NET Core. Jeśli nie, zalecamy zapoznanie się za pośrednictwem [podstawy platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Platforma ASP.NET Core w środowisku usługi Service Fabric

Aplikacje platformy ASP.NET Core i usługi Service Fabric można uruchamiać na platformy .NET Core, a także pełny program .NET Framework. Platformy ASP.NET Core można używać na dwa różne sposoby, w usłudze Service Fabric:
 - **Hostowany jako wykonywalnej gościa**. Służy to głównie uruchamianie istniejących aplikacji programu ASP.NET Core w usłudze Service Fabric, bez konieczności wprowadzania zmian kodu.
 - **Uruchamiane wewnątrz niezawodnej usługi**. Zezwala na lepszą integrację ze środowiskiem uruchomieniowym usługi Service Fabric i umożliwia usług stanowych platformy ASP.NET Core.

W pozostałej części tego artykułu opisano sposób korzystanie z platformy ASP.NET Core wewnątrz usługi Reliable Service za pomocą platformy ASP.NET Core składniki integracji, które są dostarczane z zestawem SDK usługi Service Fabric. 

## <a name="service-fabric-service-hosting"></a>Hosting usługi Service Fabric

W usłudze Service Fabric, jeden lub więcej wystąpień i/lub repliki usługi uruchamiane w *proces hosta usługi*, plik wykonywalny, który uruchamia kod usługi. Masz, jako autor usługi, proces hosta usługi i usługi Service Fabric aktywuje i monitorowanie go dla Ciebie.

Tradycyjne ASP.NET (maksymalnie MVC 5) jest ściśle powiązany w usługach IIS za pomocą System.Web.dll. Platforma ASP.NET Core rozdzielenie między serwerem sieci web i aplikacji sieci web. Zezwala aplikacji sieci web można przenosić między inne serwery internetowe i umożliwia także jako serwery sieci web *może być samodzielnie hostowane*, co oznacza można uruchomić serwera sieci web w swoim własnym procesie, w przeciwieństwie do procesu, który jest własnością dedykowanej sieci web oprogramowanie serwerowe, takich jak usługi IIS. 

Aby połączyć usługę Service Fabric i programu ASP.NET, jako wykonywalnej gościa lub w usługi Reliable Service musi być możliwe uruchomienie programu ASP.NET w procesie hosta usługi. Platforma ASP.NET Core hostingu samodzielnego umożliwia to zrobić.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hostingu platformy ASP.NET Core w usłudze Reliable Service
Zazwyczaj własne aplikacje platformy ASP.NET Core tworzenie hostem sieci Web w punkcie wejścia aplikacji, takich jak `static void Main()` method in Class metoda `Program.cs`. W tym przypadku cyklu życia hostem sieci Web jest powiązana cyklu życia procesu.

![Hostingu platformy ASP.NET Core w procesie][0]

Jednak punkt wejścia aplikacji nie jest właściwym miejscem do tworzenia hostem sieci Web w usługi Reliable Service, ponieważ punkt wejścia aplikacji tylko służy do rejestrowania typ usługi ze środowiskiem uruchomieniowym usługi Service Fabric, dzięki czemu może utworzyć wystąpienia typu usługi. Hostem sieci Web powinny być tworzone w usłudze Reliable Service sam. W ramach procesu hosta usługi wystąpień usługi i/lub repliki można przejść przez wiele cykle życia. 

Wystąpienie usługi Reliable Service jest reprezentowany przez pochodząca od klasy usługi `StatelessService` lub `StatefulService`. Stos komunikacji usługi znajduje się w `ICommunicationListener` implementacji w klasie usługi. `Microsoft.ServiceFabric.AspNetCore.*` Pakiety NuGet zawiera implementacje `ICommunicationListener` , uruchom i zarządzanie hostem sieci Web platformy ASP.NET Core w usłudze Kestrel lub słowa kluczowego HttpSys w niezawodnej usługi.

![Hostingu platformy ASP.NET Core w usłudze Reliable Service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ICommunicationListeners platformy ASP.NET Core
`ICommunicationListener` Implementacje dla słowa kluczowego HttpSys w usługach Kestrel i `Microsoft.ServiceFabric.AspNetCore.*` pakiety NuGet podobnych wzorców użycia, ale także wykonać nieco różne akcje specyficzne dla każdego serwera sieci web. 

Zarówno odbiorników komunikacji zapewniają konstruktora, który przyjmuje następujące argumenty:
 - **`ServiceContext serviceContext`**: `ServiceContext` Obiektu, który zawiera informacje dotyczące uruchomionej usługi.
 - **`string endpointName`**: nazwa `Endpoint` konfiguracji w ServiceManifest.xml. Jest to przede wszystkim, których różnią się odbiorników komunikacji dwa: Słowa kluczowego HttpSys **wymaga** `Endpoint` konfiguracji, a nie Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Wyrażenie lambda, który implementuje, w którym możesz utworzyć i zwracają `IWebHost`. Dzięki temu można skonfigurować `IWebHost` sposób zwykle w aplikacji ASP.NET Core. Wyrażenie lambda zawiera, używać adresu URL, który jest generowany dla opcje można w zależności od integracji usługi Service Fabric i `Endpoint` konfiguracji należy podać. Adres URL następnie można zmodyfikować lub używany jako — jest uruchomienie serwera sieci web.

## <a name="service-fabric-integration-middleware"></a>Oprogramowania pośredniczącego integracji usługi Service Fabric
`Microsoft.ServiceFabric.AspNetCore` Obejmuje pakiet NuGet `UseServiceFabricIntegration` metody rozszerzenia `IWebHostBuilder` , dodaje oprogramowanie pośredniczące obsługujących usługi Service Fabric. To oprogramowanie pośredniczące konfiguruje Kestrel lub słowa kluczowego HttpSys `ICommunicationListener` zarejestrować adres URL, unikatowa usługa, za pomocą usługi nazewnictwa Service Fabric i następnie weryfikuje żądań klientów, aby upewnić się, klienci nawiązywania odpowiednią usługę. Jest to konieczne w środowisku hosta udostępnione takich jak Usługa Service Fabric, gdzie wielu aplikacji sieci web można uruchamiać na takie same fizyczne lub maszyny wirtualnej, ale nie używaj nazw hostów unikatowy, aby uniemożliwić klientom przez pomyłkę połączenia z usługą problem. Ten scenariusz jest opisany bardziej szczegółowo w następnej sekcji.

### <a name="a-case-of-mistaken-identity"></a>Przypadek omyłkowe tożsamości
Repliki usługi, niezależnie od tego protokołu, nasłuchiwać IP:port unikatową kombinację. Po rozpoczęciu repliki service nasłuchiwania w punkcie końcowym IP:port zgłasza ten adres punktu końcowego do usługi Service Fabric Naming gdzie mogą być odnajdowane przez klientów lub innych usług. Użycie porty dynamicznie przypisany do aplikacji, usług repliki usługi przypadkowo może używać tego samego punktu końcowego IP:port innej usługi, która była wcześniej w tej samej fizyczne lub maszyny wirtualnej. Może to spowodować, że klient nawiązać omyłkowo niewłaściwego usługi. Może się to zdarzyć, jeśli ma miejsce następująca sekwencja zdarzeń:

 1. A usługa nasłuchuje na 10.0.0.1:30000 za pośrednictwem protokołu HTTP. 
 2. Klient jest rozpoznawana jako usługa A i pobiera 10.0.0.1:30000 adresu
 3. Usługa A przenosi się do innego węzła.
 4. Usługa B jest umieszczany na 10.0.0.1 i przypadkowo używa tego samego portu 30000.
 5. Klient próbuje nawiązać połączenie z usługi A za pomocą 10.0.0.1:30000 adres pamięci podręcznej.
 6. Klient jest teraz pomyślnie nawiązano połączenie do usługi, B, które nie chcą korzystać jest podłączone do niewłaściwej usługi.

Może to spowodować błędy w losowych odstępach czasu, które mogą być trudne do zdiagnozowania. 

### <a name="using-unique-service-urls"></a>Przy użyciu adresów URL, unikatowa usługa
Aby temu zapobiec, usługi można wpis punktu końcowego w usłudze nazewnictwa o unikatowym identyfikatorze, a następnie zweryfikować tego unikatowego identyfikatora podczas żądania klienta. To wspólne działanie między usługami w zaufanym środowisku — dzierżawy szkodliwy. To zapewnia usługa bezpiecznego uwierzytelniania w środowisku szkodliwy dzierżawy.

W zaufanym środowisku oprogramowania pośredniczącego, które jest dodawany przez `UseServiceFabricIntegration` metoda automatycznie dołącza identyfikator unikatowy adres, który jest opublikowany w usłudze nazewnictwa i sprawdza poprawność identyfikatora na każde żądanie. Jeśli identyfikator nie jest zgodny, oprogramowanie pośredniczące natychmiast zwraca odpowiedź HTTP 410 — Przeniesiono.

Usługi używające dynamicznie przypisany port należy wprowadzić korzystanie z tego oprogramowania pośredniczącego.

Usług korzystających z stały port unikatowy nie mają ten problem w środowisku współpracy. Zazwyczaj służy stały port unikatowy dla zewnętrznie połączonego z usługi, które wymagają dobrze znany port dla aplikacji klienckich nawiązać połączenie. Na przykład większość aplikacji sieci web dostępnym z Internetu użyje portu 80 i 443 dla połączeń w przeglądarce sieci web. W tym przypadku Unikatowy identyfikator nie powinna być włączona.

Na poniższym diagramie przedstawiono przepływ żądania z oprogramowaniem pośredniczącym włączone:

![Integracja platformy ASP.NET Core w usłudze Service Fabric][2]

Kestrel i słowa kluczowego HttpSys `ICommunicationListener` implementacje używać tego mechanizmu w taki sam sposób. Mimo że słowa kluczowego HttpSys wewnętrznie pozwala odróżnić żądania, w oparciu o unikatowe ścieżki adresu URL przy użyciu podstawowych *http.sys* funkcja, która funkcja jest udostępniania portów *nie* posługują się słowa kluczowego HttpSys `ICommunicationListener` Implementacja ponieważ powodującymi HTTP 503 i HTTP 404 kody stanu błędu w scenariuszu opisano wcześniej. Który z kolei utrudnia dla klientów w celu określenia celem tego błędu, HTTP 503 i HTTP 404 są już powszechnie używane do wskazać inne błędy. W efekcie Kestrel i słowa kluczowego HttpSys `ICommunicationListener` implementacje standaryzuj udostępniane przez oprogramowanie pośredniczące `UseServiceFabricIntegration` metody rozszerzenia, aby klienci należy wykonywać tylko punkt końcowy usługi ponownie rozpoznawać akcji w odpowiedzi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>Słowa kluczowego HttpSys usług Reliable Services
Słowa kluczowego HttpSys może służyć w niezawodnej usługi przez zaimportowanie **Microsoft.ServiceFabric.AspNetCore.HttpSys** pakietu NuGet. Ten pakiet zawiera `HttpSysCommunicationListener`, implementacja `ICommunicationListener`, który pozwala na tworzenie WebHost Core ASP.NET wewnątrz niezawodnej usługi przy użyciu słowa kluczowego HttpSys jako serwer sieci web.

Słowa kluczowego HttpSys jest oparta na [interfejsu API serwera HTTP Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Ta metoda korzysta z *http.sys* sterownik jądra używany przez usługi IIS na potrzeby przetwarzania żądań HTTP i przekazywać je do procesów działających aplikacji sieci web. Dzięki temu wiele procesów w tej samej fizyczne lub maszyny wirtualnej do hosta aplikacji sieci web na tym samym porcie rozróżniane przez unikatową ścieżkę adresu URL lub nazwa hosta. Funkcje te są przydatne w usłudze Service Fabric do hostowania wielu witryn sieci Web, w tym samym klastrze.

>[!NOTE]
>Implementacja słowa kluczowego HttpSys działa tylko na platformy Windows.

Na poniższym diagramie przedstawiono, jak używa słowa kluczowego HttpSys *http.sys* sterownik jądra na Windows współużytkowania portów:

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>Słowa kluczowego HttpSys usługi bezstanowej
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

### <a name="httpsys-in-a-stateful-service"></a>Słowa kluczowego HttpSys w stanowej usłudze

`HttpSysCommunicationListener` obecnie nie jest przeznaczony dla usług stanowych ze względu na kompilacji z bazowego *http.sys* funkcji udostępniania portów. Aby uzyskać więcej informacji zobacz następującą sekcję w alokacji port dynamiczny za pomocą słowa kluczowego HttpSys. W przypadku usług stanowych Kestrel jest serwer sieci web zalecane.

### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

`Endpoint` Konfiguracja jest wymagana dla serwerów sieci web, korzystających z Windows API serwera HTTP, w tym słowa kluczowego HttpSys. Serwery sieci Web, które używają interfejsu API serwera HTTP Windows najpierw należy zarezerwować swój adres URL z *http.sys* (zwykle jest to realizowane przy [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) narzędzie). Ta akcja wymaga podniesionych uprawnień, które nie mają domyślnie usług. Opcje "http" lub "https" `Protocol` właściwość `Endpoint` konfiguracji w *ServiceManifest.xml* służą specjalnie w celu poinstruowania uruchomieniowe usługi Service Fabric, aby zarejestrować adres URL z  *Sterownik HTTP.sys* w Twoim imieniu za pomocą [ *silny symbol wieloznaczny* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefiks adresu URL.

Na przykład, aby zarezerwować `http://+:80` w przypadku usługi następującej konfiguracji powinny być używane w ServiceManifest.xml:

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

#### <a name="use-httpsys-with-a-static-port"></a>Należy użyć słowa kluczowego HttpSys z portu statycznego
Aby korzystanie z portu statycznego za pomocą słowa kluczowego HttpSys, podaj numer portu w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Użyj słowa kluczowego HttpSys z portu dynamicznego
Korzystanie z portu przypisywany dynamicznie za pomocą słowa kluczowego HttpSys, Pomiń `Port` właściwość `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Port dynamiczny przydzielonej przez `Endpoint` konfiguracji zawiera tylko jeden port *na proces hosta*. Bieżący model hostingu usługi Service Fabric umożliwia wielu wystąpień usługi i/lub replik hostowane w tym samym procesie co oznacza każdy z nich współużytkują ten sam port, gdy przydzielane za pośrednictwem `Endpoint` konfiguracji. Wiele wystąpień słowa kluczowego HttpSys można współużytkować port przy użyciu podstawowych *http.sys* portu do udostępniania funkcji, ale nie jest obsługiwana przez `HttpSysCommunicationListener` ze względu na komplikacje wprowadza do obsługi żądań klientów. Użycie portu dynamicznego Kestrel jest serwer sieci web zalecane.

## <a name="kestrel-in-reliable-services"></a>Kestrel usług Reliable Services
Kestrel może służyć w niezawodnej usługi przez zaimportowanie **Microsoft.ServiceFabric.AspNetCore.Kestrel** pakietu NuGet. Ten pakiet zawiera `KestrelCommunicationListener`, implementacja `ICommunicationListener`, który pozwala na tworzenie WebHost Core ASP.NET wewnątrz niezawodnej usługi przy użyciu Kestrel jako serwer sieci web.

Kestrel jest oparte na serwerze sieci web dla wielu platform dla platformy ASP.NET Core na libuv, bibliotekę asynchronicznych operacji We/Wy dla wielu platform. W odróżnieniu od słowa kluczowego HttpSys, Kestrel nie używać scentralizowane punktu końcowego Menedżera takich jak *http.sys*. I w przeciwieństwie do słowa kluczowego HttpSys, Kestrel nie obsługuje udostępniania portów między wiele procesów. Każde wystąpienie Kestrel należy użyć unikatowy port.

![kestrel][4]

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

`Endpoint` Nazwa konfiguracji jest **nie** udostępniane `KestrelCommunicationListener` w stanowej usłudze. Jest to wyjaśnione bardziej szczegółowo w następnej sekcji.

### <a name="configure-kestrel-to-use-https"></a>Konfigurowanie usługi Kestrel do używania protokołu HTTPS
Podczas włączania protokołu Kestrel HTTPS w usłudze, należy ustawić kilka opcji nasłuchiwania.  Aktualizacja `ServiceInstanceListener` użycie punktu końcowego EndpointHttps do nasłuchiwania na konkretnym porcie (taki jak port 443). Podczas konfigurowania hosta sieci web do używania serwera Kestrel, należy skonfigurować usługi Kestrel do nasłuchiwania adresów IPv6 na wszystkich interfejsach sieciowych: 

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

Aby uzyskać pełny przykład używany w samouczku, zobacz [skonfigurować Kestrel do używania protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego
`Endpoint` Do użycia Kestrel nie jest wymagana konfiguracja. 

Kestrel jest prosty autonomiczny serwer sieci web; w odróżnieniu od słowa kluczowego HttpSys (lub HttpListener), nie musi ona `Endpoint` konfiguracji w *ServiceManifest.xml* , ponieważ nie wymaga adresu URL rejestracji przed rozpoczęciem. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel za pomocą portu statycznego
Można skonfigurować port statyczny w `Endpoint` konfiguracji ServiceManifest.xml do użytku z programem Kestrel. Chociaż nie jest to niezbędne, zawiera dwie potencjalne korzyści:
 1. Jeśli port nie należy do zakresu portów aplikacji, jest otwarty przez zaporę systemu operacyjnego przez usługę Service Fabric.
 2. Adres URL podany dla użytkownika za pośrednictwem `KestrelCommunicationListener` użyje tego portu.

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

Jeśli `Endpoint` konfiguracji nie jest używany, Pomiń nazwę w `KestrelCommunicationListener` konstruktora. W tym przypadku będzie używany port dynamiczny. Zobacz następną sekcję, aby uzyskać więcej informacji.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Kestrel za pomocą portu dynamicznego
Kestrel nie można użyć przypisania portów automatycznego z `Endpoint` konfiguracji w ServiceManifest.xml, ponieważ port automatyczne przypisanie z `Endpoint` konfiguracji przypisuje unikatowy port na *proces hosta*, i Proces hosta może zawierać wiele wystąpień Kestrel. Ponieważ Kestrel nie obsługuje udostępniania portów, to nie działa zgodnie z każdego wystąpienia Kestrel muszą zostać otwarte na unikatowy port.

Dynamiczne przypisywanie portów za pomocą Kestrel, Pomiń `Endpoint` konfiguracji w ServiceManifest.xml całkowicie i nie są przekazywane do nazwy punktu końcowego `KestrelCommunicationListener` Konstruktor:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

W tej konfiguracji `KestrelCommunicationListener` będzie automatycznie wybierać nieużywanego portu z zakresu portów aplikacji.

## <a name="service-fabric-configuration-provider"></a>Dostawca konfiguracji sieci szkieletowej usług
Konfiguracja aplikacji w programie ASP.NET Core opiera się na parach klucz wartość ustanowione przez dostawców konfiguracji, przeczytaj [konfiguracji w programie ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) Aby dowiedzieć się więcej obsługę konfiguracji platformy ASP.NET Core ogólne włączone.

W tej sekcji opisano dostawcę konfiguracji sieci szkieletowej usług integracji z konfiguracji platformy ASP.NET Core, importując `Microsoft.ServiceFabric.AspNetCore.Configuration` pakietu NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Rozszerzenia AddServiceFabricConfiguration uruchamiania
Po importu `Microsoft.ServiceFabric.AspNetCore.Configuration` pakietu NuGet, musisz zarejestrować źródła konfiguracji usługi Service Fabric za pomocą platformy ASP.NET Core konfiguracji interfejsu API przez **AddServiceFabricConfiguration** rozszerzeń w `Microsoft.ServiceFabric.AspNetCore.Configuration` przestrzeni nazw przed `IConfigurationBuilder`

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
### <a name="default-key-mapping"></a>Domyślny klucz mapowania
Domyślnie dostawca konfiguracji usługi Service Fabric zawiera nazwę pakietu, nazwę sekcji i nazwy właściwości ze sobą w celu utworzenia konfiguracji platformy asp.net core klucza przy użyciu następujących funkcji:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Na przykład, jeśli masz pakiety konfiguracji, o nazwie `MyConfigPackage` z poniżej zawartości, następnie wartość konfiguracji będzie dostępny dla platformy ASP.NET Core `IConfiguration` za pomocą klucza *MyConfigPackage:MyConfigSection:MyParameter*
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opcje konfiguracji sieci szkieletowej usługi
Dostawca konfiguracji usługi Usługa Service Fabric obsługuje także `ServiceFabricConfigurationOptions` Aby zmienić zachowanie domyślne mapowania klucza.

#### <a name="encrypted-settings"></a>Ustawienia szyfrowania
Usługa Service Fabric obsługuje do szyfrowania ustawień, konfiguracji sieci szkieletowej usługodawcy również obsługuje tę funkcję. Do bezpiecznej następować zasady domyślne, descrypted are't ustawienia szyfrowania, aby domyślnie platformy ASP.NET Core `IConfiguration`, zaszyfrowane wartości przechowywane w nim zamiast tego. Jednak aby odszyfrować wartości do przechowywania w wartości IConfiguration programu ASP.NET Core można ustawić flagi DecryptValue na wartość false w `AddServiceFabricConfiguration` rozszerzenia w następujący sposób:

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
Usługa Service Fabric obsługuje wiele konfiguracji pakietów. Domyślnie nazwa pakietu znajduje się w konfiguracji klucza. Można ustawić `IncludePackageName` flagę, aby zmienić zachowanie domyślne.
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
Oprócz powyżej 2 flagi, aby zmienić domyślne zachowanie, obsługuje również dostawcy konfiguracji sieci szkieletowej usług więcej zaawansowanych scenariuszy niestandardowe mapowanie klawiszy za pośrednictwem `ExtractKeyFunc` i niestandardowy Wyodrębnij wartości zwrotne za pośrednictwem `ExtractValueFunc`. Można nawet zmienić całego procesu, aby wypełnić danymi z konfiguracji usługi Service Fabric konfiguracja platformy ASP.NET Core za pośrednictwem `ConfigAction`.

Poniższe przykłady ilustrują, aby użyć `ConfigAction` dostosować wypełnianie danymi.
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
### <a name="configuration-update"></a>Aktualizacja konfiguracji
Dostawca konfiguracji sieci szkieletowej usługi obsługuje również aktualizacji konfiguracji i używasz platformy ASP.NET Core `IOptionsMonitor` do otrzymywania powiadomień o zmianie oraz z programem `IOptionsSnapshot` ponowne załadowanie danych konfiguracji. Aby uzyskać więcej informacji, zobacz [platformy ASP.NET Core opcje](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options).

Jest to obsługiwane przez domyślne, a żadne dodatkowe kodowania są wymagane do włączenia aktualizacji konfiguracji.

## <a name="scenarios-and-configurations"></a>Scenariusze i konfiguracji
W tej sekcji opisano następujące scenariusze i zapewnia zalecane kombinacja serwera sieci web, Konfiguracja portu, opcji integracji usługi Service Fabric i różne ustawienia, można osiągnąć usługi działają poprawnie:
 - Udostępniane zewnętrznie bezstanowa usługa ASP.NET Core
 - Bezstanowa usługa ASP.NET Core przeznaczony tylko do wewnętrznego
 - Tylko wewnętrzne usługi stanowej platformy ASP.NET Core

**Udostępniane zewnętrznie** usługi to taki, który uwidacznia punkt końcowy dostępny z poza klastrem, zwykle za pośrednictwem modułu równoważenia obciążenia.

**Wyłącznie wewnętrznym** usługa jest jedną z którego punkt końcowy tylko jest osiągalny z w ramach klastra.

> [!NOTE]
> Punkty końcowe usługi stanowe zwykle nie należy uwidaczniać w Internecie. Klastrów znajdujących się za modułem równoważenia obciążenia, które znają rozwiązania usługi Service Fabric, takich jak usługi Azure Load Balancer, nie będzie można uwidocznić usług stanowych, ponieważ nie będzie mógł zlokalizować i kierowania ruchu do odpowiedniego modułu równoważenia obciążenia Replika usługi stanowej. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Udostępniane zewnętrznie bezstanowej usługi ASP.NET Core
Kestrel jest serwer sieci web zalecane dla usług frontonu, które uwidaczniają punkty końcowe HTTP zewnętrznych, połączone z Internetem. W Windows słowa kluczowego HttpSys może służyć do zapewnienia możliwości udostępniania portów, dzięki czemu można hostować wiele usług sieci web na tym samym zestawie węzłów przy użyciu tego samego portu, zróżnicowane według nazwy hosta lub ścieżki, bez konieczności polegania na frontonu serwera proxy lub gateway zapewniają routing protokołu HTTP.
 
Gdy połączenie z Internetem, usługa bezstanowa należy używać dobrze znanych i stabilnych punktu końcowego, który jest dostępny za pośrednictwem modułu równoważenia obciążenia. Jest to adres URL podaje się użytkownicy twojej aplikacji. Zaleca się użycie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Kestrel jest serwer preferowanych sieci web, ponieważ jest on obsługiwany w Windows i Linux. |
| Konfiguracja portu | statyczna | Dobrze znanego portu statycznego powinna być skonfigurowana w `Endpoints` konfiguracji ServiceManifest.xml, takie jak 80 dla protokołu HTTP i 443 dla protokołu HTTPS. |
| ServiceFabricIntegrationOptions | Brak | `ServiceFabricIntegrationOptions.None` Opcja powinna być używana podczas konfigurowania oprogramowania pośredniczącego integracji usługi Service Fabric, dzięki czemu usługa nie jest podejmowana próba sprawdzania poprawności żądań przychodzących dla Unikatowy identyfikator. Użytkownicy zewnętrzni aplikacji nie będzie wiadomo unikatowe informacje identyfikujące używany przez oprogramowanie pośredniczące. |
| Liczba wystąpień | -1 | W typowe przypadki użycia ustawiając liczbę wystąpień powinna być równa "-1", aby wystąpienie jest dostępna we wszystkich węzłach, odbierające ruch z modułu równoważenia obciążenia. |

Jeśli wiele zewnętrznie uwidocznionych usług mają ten sam zestaw węzłów, słowa kluczowego HttpSys może służyć unikatowy, ale stabilną ścieżkę adresu URL. Można to osiągnąć przez zmodyfikowanie adresu URL podanego podczas konfigurowania IWebHost. Należy pamiętać, że dotyczy to słowa kluczowego HttpSys tylko.

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
Bezstanowej usługi, które są wywołana tylko z w ramach klastra należy używać unikatowe adresy URL i dynamicznie przypisywany portów w celu zapewnienie współpracy między wiele usług. Zaleca się użycie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Mimo że słowa kluczowego HttpSys mogą być używane w przypadku usług bezstanowych wewnętrznego, Kestrel jest zalecane serwera, aby umożliwić wiele wystąpień usługi udostępnić hosta.  |
| Konfiguracja portu | dynamicznie przypisany | Wiele replik stanowej usługi może udostępniać procesu hosta lub systemu operacyjnego hosta, a więc musi unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Za pomocą dynamiczne przypisywanie portów to ustawienie zapobiega opisanego wcześniej problemu omyłkowe tożsamości. |
| InstanceCount | dowolne | Liczba wystąpień ustawienia można ustawić dowolną wartość niezbędne w celu oferowania usługi. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Tylko do wewnętrznego stanowej usługi ASP.NET Core
Usługi stanowe, które są wywołana tylko z w ramach klastra należy używać portów przypisywany dynamicznie zapewnienie współpracy między wiele usług. Zaleca się użycie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | `HttpSysCommunicationListener` Nie jest przeznaczony do użytku przez usługi stanowe, w których replik udostępnianie procesu hosta. |
| Konfiguracja portu | dynamicznie przypisany | Wiele replik stanowej usługi może udostępniać procesu hosta lub systemu operacyjnego hosta, a więc musi unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Za pomocą dynamiczne przypisywanie portów to ustawienie zapobiega opisanego wcześniej problemu omyłkowe tożsamości. |

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
