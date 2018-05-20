---
title: Komunikacji niezawodnej usługi — omówienie | Dokumentacja firmy Microsoft
description: Omówienie modelu komunikacji niezawodnej usługi, tym odbiorników otwierania w usługach, rozpoznawania punktów końcowych i komunikacji między usługami.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 62c81368b8a3129b42262cb99cf23a5021744c1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak używać komunikacji niezawodnej usługi interfejsów API
Azure Service Fabric jako platformę jest całkowicie niezależny o komunikacji między usługami. Wszystkie protokoły i stosy są dopuszczalne, z UDP HTTP. To dewelopera usługi, aby wybrać komunikowania usług. Struktura aplikacji niezawodne usługi zawiera komunikacji wbudowanej stosach, a także interfejsów API, który można wykorzystać do tworzenia składników niestandardowych komunikacji.

## <a name="set-up-service-communication"></a>Konfigurowanie komunikacji usługi
Niezawodne interfejsu API usług używa prosty interfejs do komunikacji usługi. Aby otworzyć punktu końcowego usługi, po prostu zaimplementować ten interfejs:

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

Następnie można dodać implementacji odbiornik komunikacji, przywracając jego zastąpienie metody klasy oparta na usłudze.

Dla usługi bezstanowej:

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

Dla stanowych usług:

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

W obu przypadkach należy zwracać kolekcja odbiorników. Dzięki temu usługi do nasłuchiwania na wiele punktów końcowych potencjalnie przy użyciu różnych protokołów, za pomocą wielu odbiorników. Na przykład może mieć odbiornik HTTP i oddzielne odbiornika protokołu WebSocket. Każdy odbiornik pobiera nazwę, a wynikowy zbiór *Nazwa: adres* pary są reprezentowane jako obiekt JSON, gdy klient żąda nasłuchiwania adresów dla wystąpienia usługi lub partycji.

Usługi bezstanowej zastąpienie zwraca kolekcję ServiceInstanceListeners. A `ServiceInstanceListener` zawiera funkcję, aby utworzyć `ICommunicationListener(C#) / CommunicationListener(Java)` i nadaje mu nazwę. W przypadku usług stanowych zastąpienie zwraca kolekcję ServiceReplicaListeners. To jest nieco inne niż jego odpowiednik bezstanowych, ponieważ `ServiceReplicaListener` ma opcję, aby otworzyć `ICommunicationListener` w replikach pomocniczych. Nie można użyć wielu odbiorników komunikacji w usłudze tylko można również określić które odbiorników akceptowania żądań w replikach pomocniczych i te, które nasłuchuje repliki podstawowej.

Na przykład może mieć ServiceRemotingListener, pobierającej wywołania RPC tylko na podstawowym repliki, a drugi, niestandardowe odbiornik, który przyjmuje odczytu żądań w replikach pomocniczych za pośrednictwem protokołu HTTP:

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
> Podczas tworzenia wiele odbiorników dla usługi, każdy odbiornika **musi** należy nadać unikatową nazwę.
>
>

Ponadto opisano punkty końcowe, które są wymagane dla usługi w [manifestu usługi](service-fabric-application-and-service-manifests.md) w sekcji dla punktów końcowych.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Odbiornik komunikacji może uzyskać dostęp do zasobów punktu końcowego przydzielone z nim `CodePackageActivationContext` w `ServiceContext`. Odbiornik może następnie uruchom nasłuchiwanie żądań, gdy jest otwarty.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Zasoby punktu końcowego są wspólne dla pakietu całej usługi, a po aktywowaniu pakiet usługi są przydzielone przez sieć szkieletowa usług. Wiele replik usługi hostowanej w tej samej ServiceHost może udostępniać tego samego portu. Oznacza to, że odbiornik komunikacji powinny obsługiwać Udostępnianie portów. Zalecanym sposobem w ten sposób dotyczy odbiornik komunikacji do używania partycji identyfikator i identyfikator repliki i wystąpienia podczas generowania adresu nasłuchiwania.
>
>

### <a name="service-address-registration"></a>Rejestracja adres usługi
Wywołuje się, usługa systemowa *Naming Service* działa w klastrze usługi sieć szkieletowa usług. Usługa nazewnictwa jest rejestratora dla usług i ich adresów, które każdego wystąpienia lub repliki usługi nasłuchuje. Gdy `OpenAsync(C#) / openAsync(Java)` metody `ICommunicationListener(C#) / CommunicationListener(Java)` zakończeniu jego powrotu wartość pobiera zarejestrowane w usłudze nazw. Zwrócona wartość, która została opublikowana w usłudze nazewnictwa jest ciąg, którego wartość może być dowolna wcale. Ta wartość ciągu jest co klientów podczas ich poprosić o adres usługi z usługi nazw.

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

Usługa Service Fabric realizuje interfejsów API, która pozwala klientów i innych usług, a następnie poprosić dla tego adresu według nazwy usługi. Jest to ważne, ponieważ adres usługi nie jest statyczne. Usługi są przenoszone w klastrze do celów dostępności i równoważenia zasobów. Jest to mechanizm, który zezwala klientom na rozpoznawanie adresu nasłuchiwania dla usługi.

> [!NOTE]
> Aby uzyskać pełny przewodnik sposobu pisania odbiornik komunikacji, zobacz [usług interfejsu API sieci Web sieci szkieletowej usług za pomocą hostingu samodzielnego OWIN](service-fabric-reliable-services-communication-webapi.md) dla C#, natomiast dla języka Java można napisać własny implementację serwera HTTP, zobacz EchoServer aplikacji przykład na https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Podczas komunikacji z usługą
Niezawodne interfejsu API usług zawiera następujące biblioteki do zapisania klientów komunikujących się z usługami.

### <a name="service-endpoint-resolution"></a>Rozpoznanie punktu końcowego usługi
Pierwszy krok w celu komunikacji z usługą jest do rozpoznania adresu punktu końcowego, partycji lub wystąpienia usługi, którą chcesz rozmawiać z. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` Klasy narzędzie jest właściwością pierwotną podstawowe, ułatwiająca klientów określić punkt końcowy usługi w czasie wykonywania. W terminologii usługi sieć szkieletowa proces określania punktu końcowego usługi jest określana jako *usługi rozpoznawania punktu końcowego*.

Aby połączyć się z usługami w klastrze, ServicePartitionResolver mogą być tworzone przy użyciu ustawień domyślnych. Jest to zalecane użycie w większości sytuacji:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Aby połączyć się z usługami w innym klastrze, ServicePartitionResolver mogą być tworzone przy użyciu zestawu punktów końcowych klastra bramy. Należy pamiętać, że punkty końcowe bramy są po prostu różnych punktów końcowych do łączenia się z tym samym klastrze. Na przykład:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternatywnie `ServicePartitionResolver` można przydzielić funkcję do tworzenia `FabricClient` do wewnętrznego użycia:

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

`FabricClient` jest obiekt, który jest używany do komunikacji z klastrem usługi sieć szkieletowa usług dla różnych operacji zarządzania w klastrze. Jest to przydatne, jeśli chcesz mieć większą kontrolę nad jak program rozpoznawania nazw partycji usługi współdziała z klastra. `FabricClient` buforuje wewnętrznie i jest zwykle kosztowne, dlatego ważne jest, aby ponownie wykorzystać `FabricClient` wystąpień, jak to możliwe.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Metoda resolve należy pobrać adres usługi lub partycji usługi dla usług podzielonym na partycje.

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

Adres usługi można rozwiązać za pomocą ServicePartitionResolver, ale więcej pracy wymagane jest zapewnienie, że poprawnie można rozpoznać adresu. Klient musi wykryć, czy próba połączenia nie powiodła się z powodu błędu przejściowego i można ponowić (np. usługi przenieść lub jest tymczasowo niedostępny), lub trwały błąd (np. usługa została usunięta lub żądany zasób nie istnieje już). Wystąpienie usługi lub repliki można przenosić z węzła do węzła w dowolnym momencie kilka przyczyn. Adres usługi rozpoznawane za pomocą ServicePartitionResolver może być przestarzały przez kod klienta podejmuje próbę nawiązania połączenia czas. W takim przypadku ponownie klient musi ponownie rozpoznania adresu. Zapewnianie poprzedniej `ResolvedServicePartition` wskazuje, że program rozpoznawania nazw wymaga ponownej próby zamiast po prostu pobrać adres pamięci podręcznej.

Zazwyczaj kod klienta należy działa z ServicePartitionResolver bezpośrednio. Jest tworzony i przekazywane do fabryki klienta komunikacji niezawodnej usługi interfejsu API. Fabryk korzystanie z tym programem rozpoznawania nazw można wygenerować obiektu klienta, który może służyć do komunikacji z usługami.

### <a name="communication-clients-and-factories"></a>Komunikacja klientów i fabryki
Biblioteka fabryki komunikacji zawiera typowe wzorzec ponownych prób obsługi błędów, który ułatwia ponawianie połączeń z punktami końcowymi usług rozwiązane. Biblioteka fabryki zapewnia mechanizm ponów próbę, gdy Podaj programy obsługi błędów.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` definiuje interfejs podstawowy zaimplementowana przez fabrykę klienta komunikacji, który spowoduje utworzenie klientów, którzy mogą komunikować się z usługą sieci szkieletowej usług. Implementacja CommunicationClientFactory zależy od stosu komunikacji używane przez usługę sieć szkieletowa usług, których chce komunikacji klienta. Zapewnia niezawodne interfejsu API usług `CommunicationClientFactoryBase<TCommunicationClient>`. Udostępnia podstawową implementację interfejsu CommunicationClientFactory i wykonuje zadania, które są wspólne dla wszystkich stosy komunikacji. (Te zadania obejmują przy użyciu ServicePartitionResolver w celu określenia punktu końcowego usługi). Klienci zwykle zaimplementować klasy abstrakcyjnej CommunicationClientFactoryBase do obsługi logiki, które dotyczą stosu komunikacji.

Komunikacja klienta po prostu otrzymuje adres i używa go do łączenia się z usługą. Klient może używać dowolnego protokołu chce.

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

Fabryka klientów jest głównie odpowiedzialną za tworzenie komunikacji klientów. Dla klientów, którzy nie Obsługa połączenie trwałe, takich jak klient HTTP fabryka tylko musi utworzyć i zwracany do klienta. Inne protokoły zapewniające połączenie trwałe, takie jak niektóre protokoły binarnego, również powinny zostać zatwierdzone przez fabrykę, aby określić, czy połączenie musi zostać ponownie utworzone.  

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

Ponadto program obsługi wyjątku jest odpowiedzialny za określenie, jakie działania w sytuacji, gdy wystąpi wyjątek. Wyjątki są podzielone na **powtarzający operację** i **niepowtarzającego**.

* **Niepowtarzającego** wyjątki po prostu pobrać zgłoszony do obiektu wywołującego.
* **powtarzający operację** dalsze wyjątki są dzielone na kategorie **przejściowa** i **nieprzejściowego**.
  * **Przejściowa** wyjątki są tymi, które można po prostu ponowione bez ponowne rozpoznanie adres punktu końcowego usługi. Obejmują one przejściowe problemy z siecią lub odpowiedzi na błędy usług innych niż te, które wskazują, że adres punktu końcowego usługi nie istnieje.
  * **Inne niż przejściowy** wyjątki są te, które wymagają adres punktu końcowego usługi mają zostać rozwiązane ponownie. Obejmują one wyjątki, które wskazują, że nie można osiągnąć punktu końcowego usługi, wskazującą usługi została przeniesiona do innego węzła.

`TryHandleException` Sprawia, że decyzja o danego wyjątku. Jeśli go **nie zna** jakie należy podjąć decyzje dotyczące wyjątek, powinien on zwrócić **false**. Jeśli go **wiedzieć** jakie decyzja podejmowana, należy odpowiednio ustawić wynik i zwróć **true**.

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
### <a name="putting-it-all-together"></a>Składanie wszystkiego razem
Z `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, i `IExceptionHandler(C#) / ExceptionHandler(Java)` zbudowana wokół protokołu komunikacyjnego, `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` umieszczeniu wszystkich elementów i zawiera pętlę rozpoznawania adresów partycji usługi i obsługi błędów wokół tych składników.

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

## <a name="next-steps"></a>Kolejne kroki
* [Platformy ASP.NET Core z usługami Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Zdalne wywołania procedur z usług zdalnych niezawodne usługi](service-fabric-reliable-services-communication-remoting.md)
* [Komunikacyjny WCF za pomocą niezawodnych usług](service-fabric-reliable-services-communication-wcf.md)
