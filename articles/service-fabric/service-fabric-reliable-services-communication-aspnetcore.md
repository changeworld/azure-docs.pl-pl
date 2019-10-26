---
title: Komunikacja z usługą z ASP.NET Core | Microsoft Docs
description: Dowiedz się, jak używać ASP.NET Core w Reliable Services bezstanowe i stanowe.
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
ms.openlocfilehash: b2a1b1426af3e72756a7a85a173ef4a2a5671b02
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900192"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core na platformie Azure Service Fabric Reliable Services

ASP.NET Core to platforma o postaci Open Source i dla wielu platform. Ta struktura jest przeznaczona do tworzenia aplikacji internetowych opartych na chmurze, takich jak aplikacje internetowe, aplikacje IoT i zaplecza mobilne.

Ten artykuł zawiera szczegółowy przewodnik dotyczący hostingu usług ASP.NET Core w Service Fabric Reliable Services przy użyciu elementu **Microsoft. servicefabric. AspNetCore.** Zestaw pakietów NuGet.

Aby zapoznać się z samouczkiem wprowadzającym na ASP.NET Core w Service Fabric i instrukcje dotyczące uzyskiwania konfiguracji środowiska programistycznego, zobacz [Samouczek: Tworzenie i wdrażanie aplikacji przy ASP.NET Core użyciu usługi frontonu sieci Web API i usługi stanowej](service-fabric-tutorial-create-dotnet-app.md)zaplecza.

W dalszej części tego artykułu założono, że znasz już ASP.NET Core. Jeśli nie, zapoznaj się z tematem [podstawy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core w środowisku Service Fabric

Zarówno ASP.NET Core, jak i aplikacje Service Fabric mogą działać na platformie .NET Core lub w trybie Full .NET Framework. ASP.NET Core można użyć na dwa różne sposoby w Service Fabric:
 - **Hostowane jako plik wykonywalny gościa**. Ten sposób jest używany głównie do uruchamiania istniejących aplikacji ASP.NET Core na Service Fabric bez zmian w kodzie.
 - **Uruchom wewnątrz niezawodnej usługi**. W ten sposób można zapewnić lepszą integrację z Service Fabric środowiska uruchomieniowego i zezwala na stanowe usługi ASP.NET Core.

W dalszej części tego artykułu wyjaśniono, jak używać ASP.NET Core wewnątrz niezawodnej usługi, za pomocą składników integracji ASP.NET Core dostarczanych z zestawem SDK Service Fabric.

## <a name="service-fabric-service-hosting"></a>Service Fabric hosting usług

W Service Fabric co najmniej jedno wystąpienie i/lub repliki usługi są uruchamiane w *procesie hosta usługi*: plik wykonywalny, który uruchamia kod usługi. Ty, jako autor usługi, jest to proces hosta usługi, a Service Fabric aktywuje i monitoruje go.

Tradycyjny ASP.NET (do MVC 5) jest ściśle połączony z usługami IIS za poorednictwem system. Web. dll. ASP.NET Core zapewnia rozdzielenie między serwerem sieci Web a aplikacją sieci Web. Ta separacja pozwala na przenośne aplikacje sieci Web między różnymi serwerami sieci Web. Pozwala również na *samodzielne obsługiwanie*serwerów sieci Web. Oznacza to, że można uruchomić serwer sieci Web we własnym procesie, w przeciwieństwie do procesu, którego właścicielem jest dedykowane oprogramowanie serwera sieci Web, takie jak usługi IIS.

Aby połączyć usługę Service Fabric i ASP.NET jako plik wykonywalny gościa lub w niezawodnej usłudze, musisz mieć możliwość uruchomienia ASP.NET w ramach procesu hosta usługi. W tym celu ASP.NET Core samoobsługowego udostępniania.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting ASP.NET Core w niezawodnej usłudze
Najczęściej hostowane aplikacje ASP.NET Core tworzą element WebHost w punkcie wejścia aplikacji, na przykład metodę `static void Main()` w `Program.cs`. W takim przypadku cykl życia elementu WebHost jest związany z cyklem życia procesu.

![Hosting ASP.NET Core w procesie][0]

Jednak punkt wejścia aplikacji nie jest właściwym miejscem do utworzenia elementu WebHost w niezawodnej usłudze. Wynika to z faktu, że punkt wejścia aplikacji jest używany tylko do rejestrowania typu usługi w środowisku uruchomieniowym Service Fabric, dzięki czemu może tworzyć wystąpienia tego typu usługi. Element WebHost należy utworzyć w niezawodnej usłudze. W ramach procesu hosta usługi wystąpienia usługi i/lub repliki mogą przechodzić przez wiele cykli życia. 

Niezawodne wystąpienie usługi jest reprezentowane przez klasę usługi, która wyprowadza z `StatelessService` lub `StatefulService`. Stos komunikacyjny usługi jest zawarty w implementacji `ICommunicationListener` w klasie usługi. Pakiety NuGet `Microsoft.ServiceFabric.AspNetCore.*` zawierają implementacje `ICommunicationListener`, które uruchamiają i zarządzają ASP.NET Core WebHost dla Kestrel lub HTTP. sys w niezawodną usługę.

![Diagram służący do hostowania ASP.NET Core w niezawodnej usłudze][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
Implementacje `ICommunicationListener` dla Kestrel i HTTP. sys w pakietach NuGet `Microsoft.ServiceFabric.AspNetCore.*` mają podobne wzorce użycia. Jednak wykonują nieco inne akcje specyficzne dla każdego serwera sieci Web. 

Oba odbiorniki komunikacji zapewniają konstruktora, który przyjmuje następujące argumenty:
 - **`ServiceContext serviceContext`** : jest to obiekt `ServiceContext`, który zawiera informacje o uruchomionej usłudze.
 - **`string endpointName`** : jest to nazwa konfiguracji `Endpoint` w pliku servicemanifest. XML. Jest to głównie miejsce, w którym dwa odbiorniki komunikacji różnią się. HTTP. sys *wymaga* konfiguracji `Endpoint`, podczas gdy nie Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : jest to element lambda, który jest implementowany, w którym tworzysz i zwracasz `IWebHost`. Pozwala to na skonfigurowanie `IWebHost` w sposób zwykle w aplikacji ASP.NET Core. Lambda zawiera adres URL, który jest generowany dla Ciebie, w zależności od używanej opcji integracji Service Fabric i podanych konfiguracji `Endpoint`. Następnie można zmodyfikować ten adres URL lub użyć go do uruchomienia serwera sieci Web.

## <a name="service-fabric-integration-middleware"></a>Oprogramowanie pośredniczące Service Fabric Integration
Pakiet NuGet `Microsoft.ServiceFabric.AspNetCore` obejmuje metodę rozszerzenia `UseServiceFabricIntegration` na `IWebHostBuilder`, która dodaje oprogramowanie pośredniczące z obsługą Service Fabric. To oprogramowanie pośredniczące konfiguruje Kestrel lub HTTP. sys `ICommunicationListener`, aby zarejestrować unikatowy adres URL usługi za pomocą Usługa nazewnictwa Service Fabric. Następnie sprawdza poprawność żądań klientów, aby upewnić się, że klienci nawiązują połączenie z odpowiednią usługą. 

Ten krok jest niezbędny, aby uniemożliwić klientom błędne nawiązywanie połączenia z niewłaściwą usługą. Dzieje się tak, ponieważ w środowisku udostępnionego hosta, takim jak Service Fabric, wiele aplikacji sieci Web może działać na tej samej maszynie fizycznej lub wirtualnej, ale nie używać unikatowych nazw hostów. Ten scenariusz jest opisany bardziej szczegółowo w następnej sekcji.

### <a name="a-case-of-mistaken-identity"></a>Przypadek pomylony tożsamość
Repliki usług, niezależnie od protokołu, nasłuchiwanie na unikatowym adresie IP: kombinacja portu. Gdy replika usługi rozpoczęła nasłuchiwanie na adresie IP: Port punktu końcowego, Raport ten jest raportowany do Usługa nazewnictwa Service Fabric. Tam znajdują się klienci lub inne usługi. Jeśli usługi korzystają z dynamicznie przypisanych portów aplikacji, replika usługi może w nieprawidłowy sposób korzystać z tego samego adresu IP: Port punktu końcowego innej usługi na tej samej maszynie fizycznej lub wirtualnej. Może to spowodować, że klient nie nawiąże połączenia z złą usługą. Ten scenariusz może wystąpić w przypadku wystąpienia następującej sekwencji zdarzeń:

 1. Usługa nasłuchuje w dniu 10.0.0.1:30000 za pośrednictwem protokołu HTTP. 
 2. Klient rozpoznaje usługę A i Pobiera adres 10.0.0.1:30000.
 3. Usługa A przechodzi do innego węzła.
 4. Usługa B jest umieszczana w 10.0.0.1 i nie używa tego samego portu 30000.
 5. Klient próbuje nawiązać połączenie z usługą A z buforowanym adresem 10.0.0.1:30000.
 6. Klient pomyślnie nawiązał połączenie z usługą B, a nie jest on połączony z niewłaściwą usługą.

Może to spowodować błędy w losowych porach, które mogą być trudne do zdiagnozowania.

### <a name="using-unique-service-urls"></a>Korzystanie z unikatowych adresów URL usługi
Aby uniknąć tych usterek, usługi mogą opublikować punkt końcowy w Usługa nazewnictwa z unikatowym identyfikatorem, a następnie sprawdzić, czy unikatowy identyfikator w trakcie żądań klientów. Jest to wspólna akcja między usługami w zaufanym środowisku niebędącym niebezpiecznym dzierżawcą. Nie zapewnia bezpiecznego uwierzytelniania usługi w środowisku z dzierżawcą.

W zaufanym środowisku oprogramowanie pośredniczące dodane przez metodę `UseServiceFabricIntegration` automatycznie dołącza unikatowy identyfikator do adresu opublikowanego w Usługa nazewnictwa. Sprawdza ten identyfikator dla każdego żądania. Jeśli identyfikator nie jest zgodny, oprogramowanie pośredniczące natychmiast zwróci odpowiedź HTTP 410.

Usługi korzystające z dynamicznie przypisanego portu powinny korzystać z tego oprogramowania pośredniczącego.

Usługi korzystające ze stałego unikatowego portu nie mają tego problemu w środowisku wspólnym. Stały unikatowy port jest zwykle używany w przypadku usług zewnętrznych, które potrzebują dobrze znanego portu dla aplikacji klienckich, z którymi można nawiązać połączenie. Na przykład większość internetowych aplikacji sieci Web będzie używać portu 80 lub 443 dla połączeń przeglądarki sieci Web. W takim przypadku nie należy włączać unikatowego identyfikatora.

Na poniższym diagramie przedstawiono przepływ żądań z włączonym programem pośredniczącym:

![Service Fabric ASP.NET Core integrację][2]

Implementacje Kestrel i HTTP. sys `ICommunicationListener` używają tego mechanizmu w taki sam sposób. Mimo że plik HTTP. sys może wewnętrznie odróżnić żądania oparte na unikatowych ścieżkach URL przy użyciu podstawowej funkcji udostępniania portów **http. sys** , ta funkcja *nie* jest używana przez implementację http. sys `ICommunicationListener`. Wynika to z faktu, że kody stanu błędów HTTP 503 i HTTP 404 w scenariuszu opisanym wcześniej. Dzięki temu klienci mogą określić zamiar błędu, ponieważ HTTP 503 i HTTP 404 są często używane do wskazywania innych błędów. 

Tak więc zarówno Kestrel, jak i HTTP. sys `ICommunicationListener` normalizacji w oprogramowaniu pośredniczącym udostępnianym przez metodę rozszerzenia `UseServiceFabricIntegration`. W związku z tym klienci muszą jedynie wykonać akcję ponownego rozpoznania punktu końcowego w odpowiedzi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP. sys w Reliable Services
Można użyć pliku HTTP. sys w Reliable Services przez zaimportowanie pakietu NuGet **Microsoft. servicefabric. AspNetCore. HttpSys** . Ten pakiet zawiera `HttpSysCommunicationListener`, implementację `ICommunicationListener`. `HttpSysCommunicationListener` umożliwia utworzenie ASP.NET Core WebHost wewnątrz niezawodnej usługi przy użyciu protokołu HTTP. sys jako serwera sieci Web.

Protokół HTTP. sys jest oparty na [interfejsie API serwera HTTP systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Ten interfejs API używa sterownika jądra **http. sys** do przetwarzania żądań HTTP i kierowania ich do procesów uruchamiających aplikacje sieci Web. Pozwala to na wiele procesów na tej samej maszynie fizycznej lub wirtualnej do hostowania aplikacji sieci Web na tym samym porcie, niezależnie od unikatowej ścieżki URL lub nazwy hosta. Te funkcje są przydatne w Service Fabric do hostowania wielu witryn sieci Web w tym samym klastrze.

>[!NOTE]
>Implementacja protokołu HTTP. sys działa tylko na platformie Windows.

Na poniższym diagramie przedstawiono sposób, w jaki metoda HTTP. sys używa sterownika jądra **http. sys** w systemie Windows do udostępniania portów:

![Diagram HTTP. sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP. sys w usłudze bezstanowej
Aby użyć `HttpSys` w usłudze bezstanowej, Zastąp metodę `CreateServiceInstanceListeners` i zwróć wystąpienie `HttpSysCommunicationListener`:

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP. sys w usłudze stanowej

`HttpSysCommunicationListener` nie jest obecnie przeznaczony do użycia w usługach stanowych z powodu komplikacji z podstawową funkcją udostępniania portów **http. sys** . Aby uzyskać więcej informacji, zobacz następującą sekcję dotyczącą dynamicznego przydzielania portów za pomocą protokołu HTTP. sys. W przypadku usług stanowych Kestrel jest sugerowanym serwerem sieci Web.

### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

Konfiguracja `Endpoint` jest wymagana dla serwerów sieci Web, które używają interfejsu API serwera HTTP systemu Windows, w tym HTTP. sys. Serwery sieci Web, które używają interfejsu API serwera HTTP systemu Windows, muszą najpierw zarezerwować swój adres URL przy użyciu protokołu HTTP. sys (zazwyczaj jest to realizowane za pomocą narzędzia [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ). 

Ta akcja wymaga podwyższonego poziomu uprawnień, których usługi nie mają domyślnie. Opcje "http" lub "https" dla właściwości `Protocol` konfiguracji `Endpoint` w pliku servicemanifest. XML są używane w specjalny sposób, aby nawiązać Service Fabric środowisko uruchomieniowe w celu zarejestrowania adresu URL w usłudze HTTP. sys w Twoim imieniu. Robi to przy użyciu prefiksu [*silnego*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) adresu URL.

Na przykład aby zarezerwować `http://+:80` dla usługi, należy użyć następującej konfiguracji w pliku servicemanifest. XML:

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

A nazwa punktu końcowego musi być przeniesiona do konstruktora `HttpSysCommunicationListener`:

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

#### <a name="use-httpsys-with-a-static-port"></a>Używanie protokołu HTTP. sys z portem statycznym
Aby użyć portu statycznego z protokołem HTTP. sys, podaj numer portu w konfiguracji `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Używanie protokołu HTTP. sys z portem dynamicznym
Aby użyć dynamicznie przypisanego portu z protokołem HTTP. sys, Pomiń Właściwość `Port` w konfiguracji `Endpoint`:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Port dynamiczny przydzielony przez konfigurację `Endpoint` zapewnia tylko jeden port *na proces hosta*. Bieżący model hostingu Service Fabric umożliwia hostowanie wielu wystąpień usługi i/lub replik w tym samym procesie. Oznacza to, że każda z nich będzie współużytkować ten sam port przy przydzieleniu przez konfigurację `Endpoint`. Wiele wystąpień **http. sys** może współużytkować port przy użyciu podstawowej funkcji udostępniania portów **http. sys** . Nie jest to jednak obsługiwane przez `HttpSysCommunicationListener` ze względu na komplikacje, które wprowadza dla żądań klientów. W przypadku używania portów dynamicznych Kestrel jest sugerowanym serwerem sieci Web.

## <a name="kestrel-in-reliable-services"></a>Kestrel w Reliable Services
Kestrel można użyć w Reliable Services przez zaimportowanie pakietu NuGet **Microsoft. servicefabric. AspNetCore. Kestrel** . Ten pakiet zawiera `KestrelCommunicationListener`, implementację `ICommunicationListener`. `KestrelCommunicationListener` umożliwia utworzenie ASP.NET Core WebHost wewnątrz niezawodnej usługi za pomocą Kestrel jako serwera sieci Web.

Kestrel to Międzyplatformowy serwer sieci Web dla ASP.NET Core. W przeciwieństwie do protokołu HTTP. sys Kestrel nie używa scentralizowanego Menedżera punktów końcowych. W przeciwieństwie do protokołu HTTP. sys Kestrel nie obsługuje udostępniania portów między wieloma procesami. Każde wystąpienie elementu Kestrel musi używać unikatowego portu. Aby uzyskać więcej informacji na temat Kestrel, zobacz [szczegóły implementacji](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Diagram Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel w usłudze bezstanowej
Aby użyć `Kestrel` w usłudze bezstanowej, Zastąp metodę `CreateServiceInstanceListeners` i zwróć wystąpienie `KestrelCommunicationListener`:

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

### <a name="kestrel-in-a-stateful-service"></a>Kestrel w usłudze stanowej
Aby użyć `Kestrel` w usłudze stanowej, przesłonić metodę `CreateServiceReplicaListeners` i zwrócić wystąpienie `KestrelCommunicationListener`:

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

W tym przykładzie pojedyncze wystąpienie `IReliableStateManager` jest dostarczane do kontenera iniekcji zależności WebHost. Nie jest to bezwzględnie konieczne, ale umożliwia korzystanie z `IReliableStateManager` i niezawodnych kolekcji w metodach akcji kontrolera MVC.

*Nie* podano nazwy konfiguracji `Endpoint` do `KestrelCommunicationListener` w usłudze stanowej. Opisano to szczegółowo w poniższej sekcji.

### <a name="configure-kestrel-to-use-https"></a>Konfigurowanie usługi Kestrel do używania protokołu HTTPS
W przypadku włączania protokołu HTTPS z Kestrel w usłudze należy określić kilka opcji nasłuchiwania. Zaktualizuj `ServiceInstanceListener`, aby użyć punktu końcowego *EndpointHttps* i nasłuchiwać określonego portu (na przykład portu 443). Podczas konfigurowania hosta sieci Web do korzystania z serwera sieci Web Kestrel należy skonfigurować Kestrel do nasłuchiwania adresów IPv6 na wszystkich interfejsach sieciowych: 

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

Aby zapoznać się z pełnym przykładem w samouczku, zobacz [Konfigurowanie Kestrel do używania protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego
Konfiguracja `Endpoint` nie jest wymagana do korzystania z Kestrel. 

Kestrel to prosty autonomiczny serwer sieci Web. W przeciwieństwie do protokołu HTTP. sys (lub odbiornika HttpListener) nie wymaga konfiguracji `Endpoint` w pliku servicemanifest. XML, ponieważ nie wymaga rejestracji adresów URL przed rozpoczęciem. 

#### <a name="use-kestrel-with-a-static-port"></a>Używanie Kestrel z portem statycznym
Można skonfigurować port statyczny w konfiguracji `Endpoint` pliku servicemanifest. XML do użycia z Kestrel. Chociaż nie jest to bezwzględnie konieczne, oferuje dwie potencjalne korzyści:
 - Jeśli port nie jest objęty zakresem portów aplikacji, zostanie otwarty za pomocą zapory systemu operacyjnego przez Service Fabric.
 - Adres URL podany przez `KestrelCommunicationListener` będzie używać tego portu.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

W przypadku skonfigurowania `Endpoint` jego nazwa musi być przeniesiona do konstruktora `KestrelCommunicationListener`: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Jeśli servicemanifest. XML nie używa konfiguracji `Endpoint`, Pomiń nazwę w konstruktorze `KestrelCommunicationListener`. W takim przypadku zostanie użyty port dynamiczny. Aby uzyskać więcej informacji na ten temat, zobacz następną sekcję.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Używanie Kestrel z portem dynamicznym
Kestrel nie może użyć automatycznego przypisania portu z konfiguracji `Endpoint` w pliku servicemanifest. XML. Jest to spowodowane tym, że automatyczne przypisywanie portów z konfiguracji `Endpoint` przypisuje unikatowy port na *proces hosta*, a pojedynczy proces hosta może zawierać wiele wystąpień Kestrel. To nie działa z Kestrel, ponieważ nie obsługuje udostępniania portów. W związku z tym każde wystąpienie Kestrel musi być otwarte na unikatowym porcie.

Aby użyć dynamicznego przypisywania portów z Kestrel, pomiń konfigurację `Endpoint` w pliku servicemanifest. XML i nie przekazuj nazwy punktu końcowego do konstruktora `KestrelCommunicationListener` w następujący sposób:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

W tej konfiguracji `KestrelCommunicationListener` automatycznie wybierze nieużywany port z zakresu portów aplikacji.

W przypadku protokołu HTTPS powinien mieć punkt końcowy skonfigurowany przy użyciu protokołu HTTPS bez portu określonego w pliku servicemanifest. XML i przekazać nazwę punktu końcowego do konstruktora KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Dostawca konfiguracji Service Fabric
Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość ustanowionych przez dostawcę konfiguracji. Zapoznaj się z [konfiguracją w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) , aby dowiedzieć się więcej na temat ogólnej obsługi konfiguracji ASP.NET Core.

W tej sekcji opisano, w jaki sposób dostawca konfiguracji Service Fabric integruje się z konfiguracją ASP.NET Core przez zaimportowanie pakietu NuGet `Microsoft.ServiceFabric.AspNetCore.Configuration`.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration rozszerzenia uruchomieniowe
Po zaimportowaniu pakietu NuGet `Microsoft.ServiceFabric.AspNetCore.Configuration` należy zarejestrować Źródło konfiguracji Service Fabric za pomocą interfejsu API konfiguracji ASP.NET Core. W tym celu należy sprawdzić rozszerzenia **AddServiceFabricConfiguration** w przestrzeni nazw `Microsoft.ServiceFabric.AspNetCore.Configuration` względem `IConfigurationBuilder`.

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

Teraz usługa ASP.NET Core może uzyskać dostęp do ustawień konfiguracji Service Fabric, podobnie jak w przypadku innych ustawień aplikacji. Można na przykład użyć wzorca opcji do załadowania ustawień do obiektów o jednoznacznie określonym typie.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Domyślne mapowanie kluczy
Domyślnie dostawca konfiguracji Service Fabric obejmuje nazwę pakietu, nazwę sekcji i nazwę właściwości. Razem z tymi formularzami ASP.NET Core klucz konfiguracji:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Na przykład jeśli masz pakiet konfiguracyjny o nazwie `MyConfigPackage` z następującą zawartością, wartość konfiguracji będzie dostępna dla ASP.NET Core `IConfiguration` do *MyConfigPackage: MyConfigSection: Parameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opcje konfiguracji Service Fabric
Dostawca konfiguracji Service Fabric obsługuje także `ServiceFabricConfigurationOptions`, aby zmienić domyślne zachowanie mapowania kluczy.

#### <a name="encrypted-settings"></a>Ustawienia zaszyfrowane
Service Fabric obsługuje zaszyfrowane ustawienia, tak jak dostawca konfiguracji Service Fabric. Szyfrowane ustawienia nie są domyślnie odszyfrowywane do ASP.NET Core `IConfiguration`. W zamian zaszyfrowane wartości są przechowywane w tym miejscu. Jeśli jednak chcesz odszyfrować wartość do magazynu w ASP.NET Core IConfiguration, możesz ustawić flagę *DecryptValue* na false w rozszerzeniu `AddServiceFabricConfiguration` w następujący sposób:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Wiele pakietów konfiguracyjnych
Service Fabric obsługuje wiele pakietów konfiguracyjnych. Domyślnie nazwa pakietu jest uwzględniona w kluczu konfiguracji. Można jednak ustawić wartość false dla flagi `IncludePackageName` w następujący sposób:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Niestandardowe mapowanie kluczy, wyodrębnianie wartości i wypełnianie danych
Dostawca konfiguracji Service Fabric obsługuje również bardziej zaawansowane scenariusze umożliwiające dostosowanie mapowania kluczy za pomocą `ExtractKeyFunc` i niestandardowego — wyodrębnienie wartości przy użyciu `ExtractValueFunc`. Można nawet zmienić cały proces wypełniania danych z konfiguracji Service Fabric, aby ASP.NET Core konfigurację przy użyciu `ConfigAction`.

Poniższe przykłady ilustrują sposób użycia `ConfigAction` w celu dostosowania populacji danych:
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
Dostawca konfiguracji Service Fabric obsługuje również aktualizacje konfiguracji. Aby otrzymywać powiadomienia o zmianach, można użyć ASP.NET Core `IOptionsMonitor`, a następnie użyć `IOptionsSnapshot` do ponownego załadowania danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz [opcje ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Te opcje są domyślnie obsługiwane. W celu włączenia aktualizacji konfiguracji nie jest konieczne dalsze kodowanie.

## <a name="scenarios-and-configurations"></a>Scenariusze i konfiguracje
Ta sekcja zawiera kombinację opcji serwer sieci Web, konfiguracja portów, Service Fabric opcje integracji i różne ustawienia, które zalecamy, aby rozwiązać problemy z następującymi scenariuszami:
 - Narażone na zewnątrz ASP.NET Core usługi bezstanowe
 - Tylko wewnętrzne ASP.NET Core usługi bezstanowe
 - Tylko wewnętrzne ASP.NET Core usługi stanowe

**Zewnętrznie uwidoczniona usługa** to ta, która udostępnia punkt końcowy, który jest wywoływany z spoza klastra, zazwyczaj za pośrednictwem modułu równoważenia obciążenia.

Usługa **tylko wewnętrzna** jest tą, której punkt końcowy jest wywoływany tylko z poziomu klastra.

> [!NOTE]
> Punkty końcowe usługi stanowej zazwyczaj nie powinny być ujawnione w Internecie. Klastry z modułami równoważenia obciążenia, które nie znają rozwiązań usługi Service Fabric, takich jak Azure Load Balancer, nie będą mogły ujawniać usług stanowych. Wynika to z faktu, że moduł równoważenia obciążenia nie będzie w stanie zlokalizować i skierować ruchu do odpowiedniej repliki stanowej usługi. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Narażone na zewnątrz ASP.NET Core usługi bezstanowe
Kestrel to sugerowany serwer sieci Web dla usług frontonu, które uwidaczniają zewnętrzne, dostępne w Internecie punkty końcowe HTTP. W systemie Windows serwer HTTP. sys może zapewnić możliwość udostępniania portów, która umożliwia hostowanie wielu usług sieci Web w tym samym zestawie węzłów przy użyciu tego samego portu. W tym scenariuszu usługi sieci Web są rozróżniane przez nazwę hosta lub ścieżkę, bez polegania na serwerze proxy frontonu lub bramie do udostępniania routingu HTTP.
 
W przypadku udostępnienia Internetu usługa bezstanowa powinna używać dobrze znanego i stabilnego punktu końcowego, który jest dostępny za pomocą modułu równoważenia obciążenia. Ten adres URL należy podać użytkownikom aplikacji. Zalecamy wykonanie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Kestrel to preferowany serwer sieci Web, który jest obsługiwany w systemach Windows i Linux. |
| Konfiguracja portu | ruchom | Dobrze znany port statyczny powinien zostać skonfigurowany w konfiguracji `Endpoints` pliku servicemanifest. XML, na przykład 80 dla protokołu HTTP lub 443 dla protokołu HTTPS. |
| ServiceFabricIntegrationOptions | Brak | Użyj opcji `ServiceFabricIntegrationOptions.None` podczas konfigurowania oprogramowania pośredniczącego Service Fabric Integration, aby usługa nie podjęła próby zweryfikowania żądań przychodzących dla unikatowego identyfikatora. Użytkownicy zewnętrzni Twojej aplikacji nie będą znać unikatowych informacji identyfikacyjnych używanych przez oprogramowanie pośredniczące. |
| Liczba wystąpień | -1 | W typowych przypadkach użycia ustawienie liczba wystąpień powinna mieć wartość *-1*. Dzieje się tak, aby wystąpienie było dostępne we wszystkich węzłach, które odbierają ruch z modułu równoważenia obciążenia. |

Jeśli wiele usług narażonych na zewnątrz korzysta z tego samego zestawu węzłów, można użyć protokołu HTTP. sys z unikatową, ale stabilną ścieżką URL. Można to osiągnąć przez zmodyfikowanie adresu URL podanego podczas konfigurowania IWebHost. Należy zauważyć, że dotyczy to tylko protokołu HTTP. sys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Usługa bezstanowa ASP.NET Core tylko w ramach wewnętrznego
W przypadku usług bezstanowych, które są wywoływane tylko z klastra, należy używać unikatowych adresów URL i dynamicznie przypisywanych portów w celu zapewnienia współpracy między wieloma usługami. Zalecamy wykonanie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Chociaż można użyć protokołu HTTP. sys do wewnętrznych usług bezstanowych, Kestrel jest najlepszym serwerem, aby umożliwić wielu wystąpieniem usługi Udostępnianie hosta.  |
| Konfiguracja portu | przypisane dynamicznie | Wielokrotne repliki usługi stanowej mogą współużytkować proces hosta lub system operacyjny hosta i w ten sposób potrzebować unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | W przypadku dynamicznego przypisywania portów to ustawienie uniemożliwia opisywany wcześniej problem dotyczący tożsamości. |
| InstanceCount | Ile | Ustawienie liczby wystąpień można ustawić na dowolną wartość potrzebną do obsługi usługi. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Usługa bezstanowa ASP.NET Core tylko wewnętrznie
Usługi stanowe, które są wywoływane tylko z poziomu klastra, powinny używać dynamicznie przydzielonych portów w celu zapewnienia współpracy między wieloma usługami. Zalecamy wykonanie następującej konfiguracji:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | `HttpSysCommunicationListener` nie jest przeznaczona do użycia przez usługi stanowe, w których repliki współużytkują proces hosta. |
| Konfiguracja portu | przypisane dynamicznie | Wielokrotne repliki usługi stanowej mogą współużytkować proces hosta lub system operacyjny hosta i w ten sposób potrzebować unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | W przypadku dynamicznego przypisywania portów to ustawienie uniemożliwia opisywany wcześniej problem dotyczący tożsamości. |

## <a name="next-steps"></a>Następne kroki
[Debug your Service Fabric application by using Visual Studio (Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio)](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
