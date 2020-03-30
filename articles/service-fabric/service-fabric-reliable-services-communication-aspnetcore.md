---
title: Komunikacja serwisowa z ASP.NET Core
description: Dowiedz się, jak używać ASP.NET Core w bezstanowych i stanowych aplikacjach usług sieci szkieletowej azure.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639636"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET core w niezawodnych usługach sieci szkieletowej usług platformy Azure

ASP.NET Core to struktura open source i międzyplatformowa. Ta struktura jest przeznaczona do tworzenia aplikacji opartych na chmurze, połączonych z Internetem, takich jak aplikacje internetowe, aplikacje IoT i tylne końce mobilne.

Ten artykuł jest szczegółowym przewodnikiem po hostingu usług ASP.NET Core w niezawodnych usługach sieci szkieletowej usług przy użyciu **usługi Microsoft.ServiceFabric.AspNetCore.** zestaw pakietów NuGet.

Aby zapoznać się z samouczkiem wprowadzającym na temat ASP.NET Core w sieci szkieletowej usług i instrukcjami dotyczącymi konfigurowania środowiska programistycznego, zobacz [Samouczek: Tworzenie i wdrażanie aplikacji z usługą front-end interfejsu API sieci Web ASP.NET core i stanową usługą zaplecza](service-fabric-tutorial-create-dotnet-app.md).

W dalszej części tego artykułu założono, że jesteś już zaznajomiony z ASP.NET Core. Jeśli nie, przeczytaj [ASP.NET podstawowe](https://docs.microsoft.com/aspnet/core/fundamentals/index)podstawy .

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core w środowisku sieci szkieletowej usług

Aplikacje ASP.NET Core i Service Fabric można uruchomić w programie .NET Core lub full .NET Framework. ASP.NET Core można używać na dwa różne sposoby w sieci szkieletowej usług:
 - **Hostowany jako plik wykonywalny gościa**. W ten sposób jest używany głównie do uruchamiania istniejących aplikacji ASP.NET Core w sieci szkieletowej usług bez zmian kodu.
 - **Uruchom wewnątrz niezawodnej usługi**. W ten sposób umożliwia lepszą integrację ze środowiska uruchomieniowego sieci szkieletowej usług i umożliwia stanowe usługi ASP.NET Core.

W dalszej części tego artykułu wyjaśniono, jak używać ASP.NET Core wewnątrz niezawodnej usługi, za pośrednictwem składników integracji ASP.NET Core dostarczanych z pakietem SDK sieci szkieletowej usług.

## <a name="service-fabric-service-hosting"></a>Hosting usług sieci szkieletowej usług

W sieci szkieletowej usług jedno lub więcej wystąpień i/lub replik usługi jest uruchamianych w *procesie hosta usługi:* plik wykonywalny, który uruchamia kod usługi. Użytkownik, jako autor usługi, jest właścicielem procesu hosta usług, a usługa Service Fabric aktywuje i monitoruje go za Ciebie.

Tradycyjne ASP.NET (do MVC 5) jest ściśle powiązane z usługami IIS za pośrednictwem pliku System.Web.dll. ASP.NET Core zapewnia separację między serwerem sieci web a aplikacją sieci web. Ta separacja umożliwia aplikacjom internetowym przenośne między różnymi serwerami sieci web. Pozwala również serwery internetowe być *hostowane samodzielnie.* Oznacza to, że można uruchomić serwer sieci web we własnym procesie, w przeciwieństwie do procesu, który jest własnością dedykowanego oprogramowania serwera sieci web, takich jak IIS.

Aby połączyć usługę sieci szkieletowej usług i ASP.NET, jako plik wykonywalny gościa lub w niezawodnej usłudze, musisz mieć możliwość uruchomienia ASP.NET wewnątrz procesu hosta usługi. ASP.NET Core self-hosting pozwala to zrobić.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting ASP.NET Core w niezawodnej usłudze
Zazwyczaj samodzielnie hostowane ASP.NET aplikacje Core tworzą WebHost w punkcie wejścia aplikacji, `static void Main()` na `Program.cs`przykład metodę w . W takim przypadku cykl życia WebHost jest powiązany z cyklem życia procesu.

![Hosting ASP.NET Core w procesie][0]

Ale punkt wejścia aplikacji nie jest właściwym miejscem do tworzenia WebHost w niezawodnej usłudze. Dzieje się tak, ponieważ punkt wejścia aplikacji jest używany tylko do rejestrowania typu usługi w czasie wykonywania sieci szkieletowej usług, dzięki czemu można utworzyć wystąpienia tego typu usługi. WebHost powinny być tworzone w samej niezawodnej usługi. W ramach procesu hosta usługi wystąpienia usługi i/lub repliki mogą przechodzić przez wiele cykli życia. 

Wystąpienie niezawodnej usługi jest reprezentowane przez `StatelessService` `StatefulService`klasę usługi wynikającą z lub . Stos komunikacji dla usługi znajduje się `ICommunicationListener` w implementacji w klasie usługi. Pakiety `Microsoft.ServiceFabric.AspNetCore.*` NuGet zawierają `ICommunicationListener` implementacje tego uruchomienia i zarządzania ASP.NET Core WebHost dla Kestrel lub HTTP.sys w niezawodnej usłudze.

![Diagram hostingu ASP.NET Core w niezawodnej usłudze][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
Implementacje `ICommunicationListener` dla Kestrel i HTTP.sys w pakietach `Microsoft.ServiceFabric.AspNetCore.*` NuGet mają podobne wzorce użycia. Ale wykonują nieco inne akcje specyficzne dla każdego serwera sieci web. 

Oba detektory komunikacji zapewniają konstruktora, który przyjmuje następujące argumenty:
 - **`ServiceContext serviceContext`**: Jest `ServiceContext` to obiekt, który zawiera informacje o uruchomionej usłudze.
 - **`string endpointName`**: Jest to nazwa `Endpoint` konfiguracji w pliku ServiceManifest.xml. To przede wszystkim, gdzie dwa słuchaczy komunikacji różnią. HTTP.sys *requires* wymaga `Endpoint` konfiguracji, podczas gdy Kestrel nie.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Jest to lambda, które można zaimplementować, w którym można utworzyć i zwrócić `IWebHost`. To pozwala skonfigurować `IWebHost` sposób, w jaki normalnie w aplikacji ASP.NET Core. Lambda zawiera adres URL, który jest generowany dla Ciebie, w zależności `Endpoint` od opcji integracji sieci szkieletowej usług, których używasz i konfiguracji, którą podasz. Następnie można zmodyfikować lub użyć tego adresu URL, aby uruchomić serwer sieci web.

## <a name="service-fabric-integration-middleware"></a>Oprogramowanie pośredniczące integracji sieci szkieletowej usług
Pakiet `Microsoft.ServiceFabric.AspNetCore` NuGet zawiera `UseServiceFabricIntegration` metodę `IWebHostBuilder` rozszerzenia, która dodaje oprogramowanie pośredniczące obsługujące usługodawcę obsługującego usługę. To oprogramowanie pośredniczące konfiguruje Kestrel `ICommunicationListener` lub HTTP.sys, aby zarejestrować unikatowy adres URL usługi w usłudze nazewnictwa sieci szkieletowej. Następnie sprawdza poprawność żądań klientów, aby upewnić się, że klienci łączą się z właściwą usługą. 

Ten krok jest konieczne, aby uniemożliwić klientom omyłkowo łączenia się z niewłaściwą usługą. Dzieje się tak dlatego, że w środowisku hosta udostępnionego, takim jak sieć szkieletowa usług, wiele aplikacji sieci web może działać na tej samej maszynie fizycznej lub wirtualnej, ale nie używa unikatowych nazw hostów. Ten scenariusz jest opisany bardziej szczegółowo w następnej sekcji.

### <a name="a-case-of-mistaken-identity"></a>Przypadek błędnej tożsamości
Repliki usługi, niezależnie od protokołu, nasłuchują w unikatowej kombinacji IP:port. Po rozpoczęciu nasłuchiwania repliki usługi w punkcie końcowym ip:port raportuje ten adres punktu końcowego do usługi nazewnictwa sieci szkieletowej usług. Tam klienci lub inne usługi mogą go odkryć. Jeśli usługi używają dynamicznie przypisanych portów aplikacji, replika usługi może przypadkowo użyć tego samego punktu końcowego IP:port innej usługi wcześniej na tej samej maszynie fizycznej lub wirtualnej. Może to spowodować, że klient omyłkowo połączyć się z niewłaściwą usługą. Ten scenariusz może spowodować, jeśli wystąpi następująca sekwencja zdarzeń:

 1. Usługa A nasłuchuje w dniu 10.0.0.1:30000 za pośrednictwem protokołu HTTP. 
 2. Klient rozpoznaje usługę A i pobiera adres 10.0.0.1:30000.
 3. Usługa A przenosi się do innego węzła.
 4. Usługa B jest umieszczona na 10.0.0.1 i przypadkowo używa tego samego portu 30000.
 5. Klient próbuje połączyć się z usługą A z buforowanym adresem 10.0.0.1:30000.
 6. Klient jest teraz pomyślnie połączony z usługą B, nie zdając sobie sprawy, że jest połączony z niewłaściwą usługą.

Może to spowodować błędy w losowych momentach, które mogą być trudne do zdiagnozowania.

### <a name="using-unique-service-urls"></a>Korzystanie z unikatowych adresów URL usług
Aby zapobiec tym błędom, usługi mogą zaksięgować punkt końcowy w usłudze nazewnictwa z unikatowym identyfikatorem, a następnie sprawdzić poprawność tego unikatowego identyfikatora podczas żądań klienta. Jest to działanie współpracy między usługami w środowisku zaufanym nie wrogim dzierżawie. Nie zapewnia uwierzytelniania bezpiecznej usługi w środowisku wrogiej dzierżawy.

W zaufanym środowisku oprogramowanie pośredniczące `UseServiceFabricIntegration` dodane przez metodę automatycznie dołącza unikatowy identyfikator do adresu opublikowanego w usłudze nazewnictwa. Sprawdza poprawność tego identyfikatora dla każdego żądania. Jeśli identyfikator nie jest zgodny, oprogramowanie pośredniczące natychmiast zwraca odpowiedź HTTP 410 Gone.

Usługi korzystające z dynamicznie przypisanego portu powinny korzystać z tego oprogramowania pośredniczącego.

Usługi korzystające ze stałego unikatowego portu nie mają tego problemu w środowisku współpracy. Stały unikatowy port jest zwykle używany dla usług skierowanych na zewnątrz, które potrzebują dobrze znanego portu do łączenia się z aplikacjami klienckimi. Na przykład większość aplikacji internetowych korzystających z Internetu będzie używać portu 80 lub 443 dla połączeń przeglądarki sieci Web. W takim przypadku nie należy włączać unikatowego identyfikatora.

Na poniższym diagramie przedstawiono przepływ żądania z włączonym oprogramowaniem pośredniczącym:

![Integracja ASP.NET rdzeniem sieci szkieletowej usług][2]

Implementacje Kestrel i `ICommunicationListener` HTTP.sys używają tego mechanizmu w dokładnie taki sam sposób. Chociaż http.sys może wewnętrznie rozróżniać żądania na podstawie unikatowych ścieżek adresów URL przy *not* użyciu podstawowej funkcji udostępniania `ICommunicationListener` **portów HTTP.sys,** ta funkcja nie jest używana przez implementację HTTP.sys. To dlatego, że powoduje http 503 i HTTP 404 kody stanu błędu w scenariuszu opisanym wcześniej. To z kolei utrudnia klientom określenie intencji błędu, ponieważ HTTP 503 i HTTP 404 są często używane do wskazywania innych błędów. 

W związku z tym implementacje `ICommunicationListener` Kestrel i HTTP.sys standaryzują się w oprogramowaniu pośredniczącym dostarczonym `UseServiceFabricIntegration` przez metodę rozszerzenia. W związku z tym klienci muszą wykonać tylko akcję ponownego rozpoznawania punktu końcowego usługi na odpowiedzi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys w niezawodnych usługach
Protokołu HTTP.sys w programach Reliable Services można użyć, importując pakiet **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet. Ten pakiet `HttpSysCommunicationListener`zawiera , `ICommunicationListener`implementację . `HttpSysCommunicationListener`umożliwia utworzenie ASP.NET Core WebHost wewnątrz niezawodnej usługi przy użyciu protokołu HTTP.sys jako serwera sieci web.

Plik HTTP.sys jest zbudowany na [interfejsie API serwera HTTP systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Ten interfejs API używa sterownika jądra **HTTP.sys** do przetwarzania żądań HTTP i kierowania ich do procesów uruchamianych aplikacjami sieci web. Dzięki temu wiele procesów na tej samej maszynie fizycznej lub wirtualnej do hostowania aplikacji sieci web na tym samym porcie, rozróżniane przez unikatową ścieżkę adresu URL lub nazwę hosta. Te funkcje są przydatne w sieci szkieletowej usług do hostowania wielu witryn sieci Web w tym samym klastrze.

>[!NOTE]
>Implementacja http.sys działa tylko na platformie Windows.

Na poniższym diagramie przedstawiono, jak http.sys używa sterownika jądra **HTTP.sys** w systemie Windows do udostępniania portów:

![Diagram HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys w usłudze bezstanowej
Aby `HttpSys` użyć w usłudze bezstanowej, należy zastąpić `CreateServiceInstanceListeners` metodę i zwrócić wystąpienie: `HttpSysCommunicationListener`

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

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys w usłudze stanowej

`HttpSysCommunicationListener`nie jest obecnie przeznaczony do użytku w usługach stanowych z powodu komplikacji związanych z podstawową funkcją udostępniania **portów HTTP.sys.** Aby uzyskać więcej informacji, zobacz następującą sekcję dotyczącą dynamicznej alokacji portów za pomocą protokołu HTTP.sys. Dla usług stanowych Kestrel jest sugerowanym serwerem www.

### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

Konfiguracja `Endpoint` jest wymagana dla serwerów sieci web korzystających z interfejsu API serwera HTTP systemu Windows, w tym http.sys. Serwery sieci Web korzystające z interfejsu API serwera HTTP serwera systemu Windows muszą najpierw zarezerwować swój adres URL za pomocą protokołu HTTP.sys (zwykle odbywa się to za pomocą narzędzia [netsh).](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 

Ta akcja wymaga podwyższonych uprawnień, których usługi domyślnie nie mają. Opcje "http" lub "https" `Protocol` dla `Endpoint` właściwości konfiguracji w servicemanifest.xml są używane specjalnie do poinstruowania środowiska wykonawczego sieci szkieletowej usług, aby zarejestrować adres URL z http.sys w Twoim imieniu. Robi to przy użyciu silnego prefiksu adresu URL [*z symbolami wieloznacznych.*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)

Na przykład, `http://+:80` aby zarezerwować usługę, użyj następującej konfiguracji w pliku ServiceManifest.xml:

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

A nazwa punktu końcowego musi `HttpSysCommunicationListener` być przekazana do konstruktora:

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

#### <a name="use-httpsys-with-a-static-port"></a>Używanie protokołu HTTP.sys z portem statycznym
Aby użyć portu statycznego z http.sys, podaj numer portu w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Używanie protokołu HTTP.sys z portem dynamicznym
Aby użyć dynamicznie przypisanego portu z http.sys, należy `Port` pominąć właściwość w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Port dynamiczny przydzielony `Endpoint` przez konfigurację zapewnia tylko jeden port *na proces hosta.* Bieżący model hostingu sieci szkieletowej usług umożliwia wiele wystąpień usługi i/lub replik, które mają być hostowane w tym samym procesie. Oznacza to, że każdy z nich `Endpoint` będzie współużytkowany tego samego portu po przydzieleniu za pośrednictwem konfiguracji. Wiele **wystąpień http.sys** może współużytkować port przy użyciu podstawowej funkcji udostępniania **portów HTTP.sys.** Ale nie jest obsługiwany `HttpSysCommunicationListener` ze względu na komplikacje wprowadza dla żądań klientów. Dla dynamicznego wykorzystania portu Kestrel jest sugerowanym serwerem www.

## <a name="kestrel-in-reliable-services"></a>Pustułka w niezawodnych usługach
Pustułki można używać w programach Reliable Services, importując pakiet **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet. Ten pakiet `KestrelCommunicationListener`zawiera , `ICommunicationListener`implementację . `KestrelCommunicationListener`pozwala na utworzenie ASP.NET Core WebHost wewnątrz niezawodnej usługi przy użyciu Kestrel jako serwera www.

Pustułka to wieloplatformowy serwer www dla ASP.NET Core. W przeciwieństwie do HTTP.sys, Kestrel nie używa scentralizowanego menedżera punktów końcowych. Również w przeciwieństwie do HTTP.sys, Kestrel nie obsługuje udostępniania portów między wieloma procesami. Każde wystąpienie Kestrel musi używać unikatowego portu. Aby uzyskać więcej informacji na temat Kestrel, zobacz [szczegóły implementacji](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2).

![Schemat pustułki][4]

### <a name="kestrel-in-a-stateless-service"></a>Pustułka w służbie bezpaństwowej
Aby `Kestrel` użyć w usłudze bezstanowej, należy zastąpić `CreateServiceInstanceListeners` metodę i zwrócić wystąpienie: `KestrelCommunicationListener`

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

### <a name="kestrel-in-a-stateful-service"></a>Pustułka w służbie stanowej
Aby `Kestrel` użyć w usłudze stanowej, `CreateServiceReplicaListeners` należy zastąpić `KestrelCommunicationListener` metodę i zwrócić wystąpienie:

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

W tym przykładzie pojedyncze `IReliableStateManager` wystąpienie jest dostarczane do kontenera iniekcji zależności WebHost. Nie jest to absolutnie konieczne, ale `IReliableStateManager` umożliwia użycie i niezawodne kolekcje w metodach akcji kontrolera MVC.

Nazwa `Endpoint` konfiguracji *nie* jest `KestrelCommunicationListener` podana w usłudze stanowej. Jest to wyjaśnione bardziej szczegółowo w poniższej sekcji.

### <a name="configure-kestrel-to-use-https"></a>Konfigurowanie usługi Kestrel do używania protokołu HTTPS
Po włączeniu protokołu HTTPS z kestrelem w usłudze należy ustawić kilka opcji nasłuchiwania. Zaktualizuj, `ServiceInstanceListener` aby użyć punktu końcowego *endpointhttps* i nasłuchiwać na określonym porcie (na przykład port 443). Podczas konfigurowania hosta internetowego do korzystania z serwera sieci Web Kestrel należy skonfigurować Kestrel do nasłuchiwania adresów IPv6 we wszystkich interfejsach sieciowych: 

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

Pełny przykład w samouczku można znaleźć w temacie [Konfigurowanie pustułki do używania protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego
Konfiguracja `Endpoint` nie jest wymagana do używania Pustułka. 

Pustułka to prosty samodzielny serwer www. W przeciwieństwie do HTTP.sys (lub HttpListener), nie wymaga `Endpoint` konfiguracji w ServiceManifest.xml, ponieważ nie wymaga rejestracji adresu URL przed rozpoczęciem. 

#### <a name="use-kestrel-with-a-static-port"></a>Użyj pustułki z portem statycznym
Można skonfigurować port statyczny `Endpoint` w konfiguracji ServiceManifest.xml do użytku z Kestrel. Mimo, że nie jest to absolutnie konieczne, oferuje dwie potencjalne korzyści:
 - Jeśli port nie mieści się w zakresie portów aplikacji, jest otwierany za pośrednictwem zapory systemu operacyjnego przez usługę Service Fabric.
 - Adres URL podany `KestrelCommunicationListener` za pośrednictwem będzie korzystać z tego portu.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Jeśli `Endpoint` jest skonfigurowany, jego nazwa musi `KestrelCommunicationListener` być przekazana do konstruktora: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Jeśli ServiceManifest.xml nie używa `Endpoint` konfiguracji, należy pominąć `KestrelCommunicationListener` nazwę w konstruktorze. W takim przypadku użyje portu dynamicznego. Zobacz następną sekcję, aby uzyskać więcej informacji na ten temat.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Użyj pustułki z dynamicznym portem
Pustułka nie może używać automatycznego `Endpoint` przypisania portu z konfiguracji w pliku ServiceManifest.xml. Dzieje się tak dlatego, `Endpoint` że automatyczne przypisywanie portów z konfiguracji przypisuje unikatowy port na *proces hosta,* a pojedynczy proces hosta może zawierać wiele wystąpień Kestrel. To nie działa z Kestrel, ponieważ nie obsługuje udostępniania portów. W związku z tym każde wystąpienie Kestrel musi być otwarte na unikatowym porcie.

Aby użyć dynamicznego przypisania portów `Endpoint` z Kestrel, należy całkowicie pominąć konfigurację w pliku ServiceManifest.xml i nie przekazywać nazwy punktu końcowego konstruktorowi `KestrelCommunicationListener` w następujący sposób:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

W tej `KestrelCommunicationListener` konfiguracji automatycznie wybierze nieużyny port z zakresu portów aplikacji.

W przypadku protokołu HTTPS punkt końcowy powinien być skonfigurowany za pomocą protokołu HTTPS bez portu określonego w pliku ServiceManifest.xml i przekazać nazwę punktu końcowego konstruktorowi KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Dostawca konfiguracji sieci szkieletowej usług
Konfiguracja aplikacji w ASP.NET Core opiera się na parach klucz-wartość ustanowiona przez dostawcę konfiguracji. Przeczytaj [konfigurację w ASP.NET Core,](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) aby dowiedzieć się więcej na temat ogólnej obsługi konfiguracji ASP.NET Core.

W tej sekcji opisano, jak dostawca konfiguracji sieci szkieletowej usług `Microsoft.ServiceFabric.AspNetCore.Configuration` integruje się z konfiguracją ASP.NET Core, importując pakiet NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Rozszerzenia uruchamiania AddServiceFabricConfiguration
Po zaimportowaniu pakietu `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet należy zarejestrować źródło konfiguracji sieci szkieletowej usług w interfejsie API konfiguracji ASP.NET Core. Można to zrobić, sprawdzając **rozszerzenia AddServiceFabricConfiguration** w obszarze `Microsoft.ServiceFabric.AspNetCore.Configuration` nazw względem `IConfigurationBuilder`.

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

Teraz usługa ASP.NET Core może uzyskać dostęp do ustawień konfiguracji sieci szkieletowej usług, podobnie jak do innych ustawień aplikacji. Na przykład można użyć wzorca opcji, aby załadować ustawienia do silnie wpisanych obiektów.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Domyślne mapowanie klawiszy
Domyślnie dostawca konfiguracji sieci szkieletowej usług zawiera nazwę pakietu, nazwę sekcji i nazwę właściwości. Razem tworzą one klucz konfiguracji ASP.NET Core w następujący sposób:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Na przykład, jeśli masz pakiet `MyConfigPackage` konfiguracyjny o nazwie o następującej zawartości, `IConfiguration` wartość konfiguracji będzie dostępna na ASP.NET Core za pośrednictwem *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opcje konfiguracji sieci szkieletowej usług
Dostawca konfiguracji sieci szkieletowej usług obsługuje `ServiceFabricConfigurationOptions` również zmianę domyślnego zachowania mapowania kluczy.

#### <a name="encrypted-settings"></a>Ustawienia zaszyfrowane
Sieć szkieletowa usług obsługuje zaszyfrowane ustawienia, podobnie jak dostawca konfiguracji sieci szkieletowej usług. Zaszyfrowane ustawienia nie są domyślnie odszyfrowywane, aby ASP.NET Core. `IConfiguration` Zamiast tego są tam przechowywane zaszyfrowane wartości. Ale jeśli chcesz odszyfrować wartość do przechowywania w ASP.NET Core IConfiguration, można ustawić *OdszyfrowaćZagianie* na false w `AddServiceFabricConfiguration` rozszerzeniu, w następujący sposób:

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
Usługa Fabric obsługuje wiele pakietów konfiguracyjnych. Domyślnie nazwa pakietu jest uwzględniona w kluczu konfiguracji. Ale można ustawić `IncludePackageName` flagę na false, w następujący sposób:
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Niestandardowe mapowanie kluczy, wyodrębnianie wartości i zasiew danych
Dostawca konfiguracji sieci szkieletowej usług obsługuje również bardziej zaawansowane `ExtractKeyFunc` scenariusze dostosowywania `ExtractValueFunc`mapowania kluczy za pomocą programu . Można nawet zmienić cały proces wypełniania danych z konfiguracji sieci szkieletowej `ConfigAction`usług na ASP.NET konfigurację rdzenia za pomocą .

Poniższe przykłady ilustrują `ConfigAction` sposób użycia do dostosowywania populacji danych:
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
Dostawca konfiguracji sieci szkieletowej usług obsługuje również aktualizacje konfiguracji. Za pomocą ASP.NET Core `IOptionsMonitor` można odbierać powiadomienia o `IOptionsSnapshot` zmianach, a następnie używać do ponownego ładowania danych konfiguracji. Aby uzyskać więcej informacji, zobacz [ASP.NET Opcje podstawowe](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Te opcje są domyślnie obsługiwane. Dalsze kodowanie nie jest potrzebne do włączania aktualizacji konfiguracji.

## <a name="scenarios-and-configurations"></a>Scenariusze i konfiguracje
Ta sekcja zawiera kombinację serwera sieci web, konfiguracji portów, opcji integracji sieci szkieletowej usług i różnych ustawień, które zaleca się rozwiązywanie następujących problemów z następującymi scenariuszami:
 - Zewnętrznie narażone ASP.NET Podstawowe usługi bezstanowe
 - Usługi bezstanowe tylko do ASP.NET wewnętrznego
 - Wewnętrzne usługi tylko ASP.NET podstawowe usługi stanowe

**Zewnętrznie uwidaczniana usługa** to taka, która udostępnia punkt końcowy, który jest wywoływany spoza klastra, zwykle za pośrednictwem modułu równoważenia obciążenia.

Usługa **tylko wewnętrzna** jest taki, którego punkt końcowy jest wywoływany tylko z wewnątrz klastra.

> [!NOTE]
> Punkty końcowe usługi stanowej zazwyczaj nie powinny być narażone na działanie Internetu. Klastry za modułami równoważenia obciążenia, które nie są świadome rozpoznawania usług sieci szkieletowej usług, takich jak Moduł równoważenia obciążenia platformy Azure, nie będą mogły udostępnić usług stanowych. To dlatego, że moduł równoważenia obciążenia nie będzie w stanie zlokalizować i trasy ruchu do odpowiedniej repliki usługi stanowej. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Zewnętrznie narażone ASP.NET Podstawowe usługi bezstanowe
Pustułka to sugerowany serwer sieci web dla usług frontonu, które ujawniają zewnętrzne punkty końcowe HTTP skierowane do Internetu. W systemie Windows http.sys może zapewnić możliwość udostępniania portów, co pozwala na hostowanie wielu usług sieci web w tym samym zestawie węzłów przy użyciu tego samego portu. W tym scenariuszu usługi sieci web są zróżnicowane według nazwy hosta lub ścieżki, bez polegania na serwerze proxy lub bramie frontonalnej w celu zapewnienia routingu HTTP.
 
Po wystawieniu na działanie Internetu usługa bezstanowa powinna używać dobrze znanego i stabilnego punktu końcowego, który jest osiągalny za pośrednictwem modułu równoważenia obciążenia. Ten adres URL zostanie podaj użytkownikom aplikacji. Zalecamy następującą konfigurację:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Pustułka jest preferowanym serwerem www, ponieważ jest obsługiwany przez Windows i Linux. |
| Konfiguracja portu | static | Dobrze znany port statyczny powinien być `Endpoints` skonfigurowany w konfiguracji ServiceManifest.xml, na przykład 80 dla HTTP lub 443 dla HTTPS. |
| ServiceFabricIntegrationOptions (UsługiFabricIntegrationOptions) | Brak | Użyj `ServiceFabricIntegrationOptions.None` tej opcji podczas konfigurowania oprogramowania pośredniczącego integracji sieci szkieletowej usług, aby usługa nie próbowała sprawdzić poprawność przychodzących żądań dla unikatowego identyfikatora. Zewnętrzni użytkownicy aplikacji nie znają unikatowych informacji identyfikujących używanego przez oprogramowanie pośredniczące. |
| Liczba wystąpień | -1 | W typowych przypadkach użycia ustawienie liczby wystąpień powinno być ustawione na *-1*. Odbywa się to tak, aby wystąpienie było dostępne we wszystkich węzłach, które odbierają ruch z modułu równoważenia obciążenia. |

Jeśli wiele usług udostępnianych zewnętrznie współużytkuje ten sam zestaw węzłów, można użyć protokołu HTTP.sys z unikatową, ale stabilną ścieżką adresu URL. Można to osiągnąć, modyfikując adres URL podany podczas konfigurowania IWebHost. Należy pamiętać, że dotyczy to tylko protokołu HTTP.sys.

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

### <a name="internal-only-stateless-aspnet-core-service"></a>Usługa ASP.NET Core tylko wewnątrz wewnętrznego
Usługi bezstanowe, które są wywoływane tylko z poziomu klastra, powinny używać unikatowych adresów URL i dynamicznie przypisywanych portów w celu zapewnienia współpracy między wieloma usługami. Zalecamy następującą konfigurację:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Chociaż można użyć http.sys dla wewnętrznych usług bezstanowych, Kestrel jest najlepszym serwerem, aby umożliwić wiele wystąpień usługi do udostępniania hosta.  |
| Konfiguracja portu | dynamicznie przypisane | Wiele replik usługi stanowej może współużytkować proces hosta lub system operacyjny hosta, a zatem będzie wymagać unikatowych portów. |
| ServiceFabricIntegrationOptions (UsługiFabricIntegrationOptions) | UżyjUniqueServiceUrl | W przypadku dynamicznego przypisywania portów to ustawienie zapobiega opisanemu wcześniej problemowi z błędną tożsamością. |
| Liczba wystąpień | Wszelki | Ustawienie liczby wystąpień można ustawić na dowolną wartość niezbędną do obsługi usługi. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Usługa ASP.NET Core tylko wewnętrznego
Usługi stanowe, które są wywoływane tylko z poziomu klastra, powinny używać dynamicznie przypisanych portów w celu zapewnienia współpracy między wieloma usługami. Zalecamy następującą konfigurację:

|  |  | **Uwagi** |
| --- | --- | --- |
| Serwer sieci Web | Kestrel | Nie `HttpSysCommunicationListener` jest przeznaczony do użytku przez usługi stanowe, w którym repliki współużytkują proces hosta. |
| Konfiguracja portu | dynamicznie przypisane | Wiele replik usługi stanowej może współużytkować proces hosta lub system operacyjny hosta, a zatem będzie wymagać unikatowych portów. |
| ServiceFabricIntegrationOptions (UsługiFabricIntegrationOptions) | UżyjUniqueServiceUrl | W przypadku dynamicznego przypisywania portów to ustawienie zapobiega opisanemu wcześniej problemowi z błędną tożsamością. |

## <a name="next-steps"></a>Następne kroki
[Debug your Service Fabric application by using Visual Studio (Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio)](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
