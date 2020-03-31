---
title: Omówienie komunikacji w zakresie niezawodnych usług
description: Omówienie modelu komunikacji reliable services, w tym otwieranie odbiorników w usługach, rozpoznawanie punktów końcowych i komunikowanie się między usługami.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 3c1a6cfa5227369bf1cde4af087019727c22c0c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462953"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak korzystać z interfejsów API komunikacji niezawodnych usług
Usługa Azure Service Fabric jako platforma jest całkowicie niezależna od komunikacji między usługami. Wszystkie protokoły i stosy są dopuszczalne, od UDP do HTTP. To do dewelopera usługi, aby wybrać, jak usługi powinny komunikować się. Struktura aplikacji niezawodne usługi zawiera wbudowane stosy komunikacji, a także interfejsy API, których można użyć do tworzenia składników komunikacji niestandardowej.

## <a name="set-up-service-communication"></a>Konfigurowanie komunikacji serwisowej
Interfejs API niezawodnych usług używa prostego interfejsu do komunikacji usługi. Aby otworzyć punkt końcowy dla usługi, wystarczy zaimplementować ten interfejs:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Następnie można dodać implementacji odbiornika komunikacji, zwracając go w zastąpieniu metody klasy opartej na usłudze.

Dla usług bezstanowych:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

W przypadku usług stanowych:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

W obu przypadkach zwracasz kolekcję odbiorników. Dzięki temu usługa nasłuchiwać w wielu punktach końcowych, potencjalnie przy użyciu różnych protokołów, przy użyciu wielu odbiorników. Na przykład może mieć odbiornik HTTP i oddzielny odbiornik WebSocket. Każdy odbiornik otrzymuje nazwę, a wynikowa kolekcja nazw: pary *adresów* są reprezentowane jako obiekt JSON, gdy klient żąda adresów nasłuchujących dla wystąpienia usługi lub partycji.

W usłudze bezstanowej zastąpienie zwraca kolekcję ServiceInstanceListeners. A `ServiceInstanceListener` zawiera funkcję do `ICommunicationListener(C#) / CommunicationListener(Java)` tworzenia i nadaje mu nazwę. W przypadku usług stanowych zastępowanie zwraca kolekcję ServiceReplicaListeners. To jest nieco różni się od `ServiceReplicaListener` jego odpowiednik bezstanowy, ponieważ ma możliwość otwarcia `ICommunicationListener` na repliki pomocnicze. Nie tylko można używać wielu odbiorników komunikacji w usłudze, ale można również określić, które odbiorniki akceptują żądania replik pomocniczych, a które nasłuchują tylko w replikach podstawowych.

Na przykład można mieć ServiceRemotingListener, który przyjmuje wywołania RPC tylko na replikach podstawowych, a drugi, niestandardowy odbiornik, który przyjmuje żądania odczytu w replikach pomocniczych za pośrednictwem protokołu HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Podczas tworzenia wielu odbiorników dla usługi, każdy odbiornik **musi** mieć unikatową nazwę.
>
>

Na koniec należy opisać punkty końcowe, które są wymagane dla usługi w [manifeście usługi](service-fabric-application-and-service-manifests.md) w sekcji na punkty końcowe.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Odbiornik komunikacji może uzyskać dostęp do zasobów punktu `CodePackageActivationContext` końcowego przydzielonych do niego z w `ServiceContext`. Odbiornik można następnie rozpocząć nasłuchiwanie żądań po jego otwarciu.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Zasoby punktu końcowego są wspólne dla całego pakietu usług i są przydzielane przez sieć szkieletową usług, gdy pakiet usług jest aktywowany. Wiele replik usługi hostowanych w tym samym serwisie ServiceHost może współużytkować ten sam port. Oznacza to, że odbiornik komunikacji powinien obsługiwać udostępnianie portów. Zalecanym sposobem jest dla odbiornika komunikacji, aby użyć identyfikator partycji i identyfikator repliki/wystąpienia, gdy generuje adres nasłuchiwać.
>
>

### <a name="service-address-registration"></a>Rejestracja adresu usługi
Usługa systemowa o nazwie *Usługa nazewnictwa* działa w klastrach sieci szkieletowej usług. Usługa nazewnictwa jest rejestratorem usług i ich adresów, na które nasłuchuje każde wystąpienie lub replika usługi. Po `OpenAsync(C#) / openAsync(Java)` `ICommunicationListener(C#) / CommunicationListener(Java)` zakończeniu metody, jego wartość zwracana zostanie zarejestrowana w usłudze nazewnictwa. Ta zwracana wartość, która zostanie opublikowana w usłudze nazewnictwa jest ciągiem, którego wartość może być w ogóle. Ta wartość ciągu jest to, co klienci widzą, gdy proszą o adres dla usługi z usługi nazewnictwa.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Sieci szkieletowej usług udostępnia interfejsy API, które umożliwiają klientom i innym usługom następnie poprosić o ten adres według nazwy usługi. Jest to ważne, ponieważ adres usługi nie jest statyczny. Usługi są przenoszone w klastrze do celów równoważenia zasobów i dostępności. Jest to mechanizm, który umożliwia klientom rozpoznanie adresu nasłuchiwania usługi.

> [!NOTE]
> Aby uzyskać pełny sposób pisania odbiornika komunikacji, zobacz [Usługi interfejsu API sieci Web sieci szkieletowej usług usługi OWIN z własnym hostingiem](service-fabric-reliable-services-communication-webapi.md) dla języka https://github.com/Azure-Samples/service-fabric-java-getting-startedC#, natomiast w przypadku języka Java można napisać własną implementację serwera HTTP, zobacz przykład aplikacji EchoServer w .
>
>

## <a name="communicating-with-a-service"></a>Komunikowanie się z usługą
Interfejs API usług niezawodnych udostępnia następujące biblioteki do zapisywania klientów komunikuje się z usługami.

### <a name="service-endpoint-resolution"></a>Rozpoznawanie punktów końcowych usługi
Pierwszym krokiem do komunikacji z usługą jest rozpoznanie adresu punktu końcowego partycji lub wystąpienia usługi, z którą chcesz porozmawiać. Klasa `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` narzędzia jest podstawowym prymitywne, który pomaga klientom określić punkt końcowy usługi w czasie wykonywania. W terminologii sieci szkieletowej usług proces określania punktu końcowego usługi jest określany jako *rozpoznawanie punktu końcowego usługi*.

Aby połączyć się z usługami w klastrze, ServicePartitionResolver można utworzyć przy użyciu ustawień domyślnych. Jest to zalecane użycie w większości sytuacji:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Aby połączyć się z usługami w innym klastrze, ServicePartitionResolver można utworzyć z zestawem punktów końcowych bramy klastra. Należy zauważyć, że punkty końcowe bramy są po prostu różne punkty końcowe do łączenia się z tym samym klastrem. Przykład:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternatywnie, `ServicePartitionResolver` może mieć funkcję tworzenia `FabricClient` do użycia wewnętrznie:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient`jest obiektem używanym do komunikowania się z klastrem sieci szkieletowej usług dla różnych operacji zarządzania w klastrze. Jest to przydatne, gdy chcesz mieć większą kontrolę nad interakcja rozpoznawania partycji usługi z klastrem. `FabricClient`wykonuje buforowanie wewnętrznie i jest na ogół kosztowne do utworzenia, więc ważne jest, aby ponownie użyć `FabricClient` wystąpień jak najwięcej.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Metoda rozpoznawania jest następnie używana do pobierania adresu usługi lub partycji usługi dla usług podzielonych na partycje.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Adres usługi można łatwo rozwiązać za pomocą ServicePartitionResolver, ale więcej pracy jest wymagane, aby upewnić się, że rozwiązany adres może być używany poprawnie. Klient musi wykryć, czy próba połączenia nie powiodła się z powodu błędu przejściowego i może zostać ponowiona (np. usługa przeniesiona lub jest tymczasowo niedostępna) lub trwałego błędu (np. usługa została usunięta lub żądany zasób już nie istnieje). Wystąpienia usługi lub repliki mogą poruszać się z węzła do węzła w dowolnym momencie z wielu powodów. Adres usługi rozwiązany za pośrednictwem ServicePartitionResolver może być przestarzały w czasie, gdy kod klienta próbuje się połączyć. W takim przypadku ponownie klient musi ponownie rozpoznać adres. Podanie poprzedniego `ResolvedServicePartition` wskazuje, że program rozpoznawania nazw musi spróbować ponownie, a nie po prostu pobrać buforowany adres.

Zazwyczaj kod klienta nie musi pracować z ServicePartitionResolver bezpośrednio. Jest tworzony i przekazywany do fabryk klientów komunikacji w interfejsie API usług niezawodnych. Fabryki używają rozpoznawania rozpoznawania rozpoznawania wewnętrznie do generowania obiektu klienta, który może służyć do komunikowania się z usługami.

### <a name="communication-clients-and-factories"></a>Klienci komunikacji i fabryki
Biblioteka fabryki komunikacji implementuje typowy wzorzec ponawiania prób obsługi błędów, który ułatwia ponawianie połączeń z rozwiązanymi punktami końcowymi usługi. Biblioteka fabryczna udostępnia mechanizm ponawiania próby podczas podawania programów obsługi błędów.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`definiuje interfejs podstawowy zaimplementowany przez fabrykę klienta komunikacji, która tworzy klientów, którzy mogą rozmawiać z usługą sieci szkieletowej usług. Implementacja CommunicationClientFactory zależy od stosu komunikacji używanego przez usługę sieci szkieletowej usług, w której klient chce się komunikować. Interfejs API niezawodnych `CommunicationClientFactoryBase<TCommunicationClient>`usług zapewnia plik . Zapewnia to podstawową implementację interfejsu CommunicationClientFactory i wykonuje zadania, które są wspólne dla wszystkich stosów komunikacji. (Te zadania obejmują przy użyciu ServicePartitionResolver do określenia punktu końcowego usługi). Klienci zwykle implementują abstrakcyjną klasę CommunicationClientFactoryBase do obsługi logiki, która jest specyficzna dla stosu komunikacji.

Klient komunikacji po prostu odbiera adres i używa go do łączenia się z usługą. Klient może używać dowolnego protokołu, który chce.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

Fabryka klienta jest przede wszystkim odpowiedzialna za tworzenie klientów komunikacyjnych. Dla klientów, którzy nie utrzymują trwałego połączenia, takiego jak klient HTTP, fabryka musi tylko utworzyć i zwrócić klienta. Inne protokoły, które utrzymują trwałe połączenie, takie jak niektóre protokoły binarne, powinny również zostać sprawdzone przez fabrykę, aby ustalić, czy połączenie musi zostać ponownie utworzone.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Na koniec program obsługi wyjątków jest odpowiedzialny za określenie, jakie działania należy podjąć, gdy wystąpi wyjątek. Wyjątki są podzielone na **ponowienie próby** i **nie można ponowić**próby .

* **Wyjątki, które nie można ponowić** po prostu uzyskać rethrown z powrotem do wywołującego.
* wyjątki, które **można ponowić,** są dalej klasyfikowane do **stanu przejściowego** i **nieprzejednego**.
  * **Wyjątki przejściowe** są te, które można po prostu ponowione bez ponownego rozpoznawania adresu punktu końcowego usługi. Będą one obejmować przejściowe problemy z siecią lub odpowiedzi na błędy usługi inne niż te, które wskazują, że adres punktu końcowego usługi nie istnieje.
  * **Wyjątki nieprzemijające** to te, które wymagają ponownego rozwiązania adresu punktu końcowego usługi. Należą do nich wyjątki, które wskazują, że nie można osiągnąć punktu końcowego usługi, wskazując, że usługa została przeniesiona do innego węzła.

Podejmuje `TryHandleException` decyzję o danym wyjątku. Jeśli **nie wie,** jakie decyzje podjąć w sprawie wyjątku, powinien zwrócić **fałszywe**. Jeśli **wie,** jaką decyzję podjąć, należy odpowiednio ustawić wynik i powrócić **do rzeczywistości.**

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Z `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`i `IExceptionHandler(C#) / ExceptionHandler(Java)` zbudowany wokół protokołu `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` komunikacyjnego, zawija to wszystko razem i zapewnia obsługę błędów i pętli rozdzielczości adres partycji usługi wokół tych składników.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Następne kroki
* [ASP.NET Core z niezawodnymi usługami](service-fabric-reliable-services-communication-aspnetcore.md)
* [Zdalne wywołania procedury z obsługą komunikacji zdalnej z niezawodnymi usługami](service-fabric-reliable-services-communication-remoting.md)
* [Komunikacja WCF przy użyciu niezawodnych usług](service-fabric-reliable-services-communication-wcf.md)
