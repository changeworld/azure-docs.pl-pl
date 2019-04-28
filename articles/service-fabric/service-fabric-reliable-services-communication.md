---
title: Omówienie komunikacji usług Reliable Services | Dokumentacja firmy Microsoft
description: Omówienie modelu komunikacji usług Reliable Services, tym odbiorników otwierania w usługach, rozpoznawania punktów końcowych i komunikacji między usługami.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 15b45cadc69830827952d87ffc2315b06b07b02c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124993"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak używać interfejsów API komunikacji usług Reliable Services
Usługa Azure Service Fabric jako platforma jest całkowicie niezależny od o komunikacji między usługami. Wszystkie protokoły i stosów są dopuszczalne, z UDP, HTTP. To Ty dla deweloperów usługi do wyboru komunikowania usług. Struktura aplikacji usług Reliable Services zapewnia stosów komunikacji wbudowanej, a także interfejsów API, które służy do tworzenia składników niestandardowych komunikacji.

## <a name="set-up-service-communication"></a>Konfigurowanie komunikacji usług
Niezawodne interfejsu API usługi używa prosty interfejs do komunikacji usługi. Aby otworzyć punkt końcowy dla Twojej usługi, po prostu implementować ten interfejs:

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

Następnie można dodać implementacji odbiornika komunikacji, przywracając jego zastąpienie metody klasy oparta na usłudze.

W przypadku usług bezstanowych:

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

W obu przypadkach zwracana jest kolekcja odbiorników. Dzięki temu usługi w celu nasłuchiwania na wiele punktów końcowych, potencjalnie przy użyciu różnych protokołów, korzystając z wielu odbiorników. Na przykład mogą mieć odbiornika protokołu HTTP i oddzielnych odbiornika protokołu WebSocket. Każdy odbiornik pobiera nazwę, a wynikowy zbiór *Nazwa: adres* pary są reprezentowane jako obiekt JSON, gdy klient zażąda nasłuchiwania adresów dla wystąpienia usługi lub partycji.

Usługi bezstanowej zastąpienie zwraca kolekcję ServiceInstanceListeners. A `ServiceInstanceListener` zawiera funkcję, aby utworzyć `ICommunicationListener(C#) / CommunicationListener(Java)` i nadaje jej nazwę. Dla usług stanowych zastąpienie zwraca kolekcję ServiceReplicaListeners. To różni się nieco od jego odpowiednika bezstanowych, ponieważ `ServiceReplicaListener` ma opcję otwarcia `ICommunicationListener` w replikach pomocniczych. Nie można użyć wielu odbiorników komunikacji w usłudze tylko można również określić które odbiorników akceptowania żądań w replikach pomocniczych i te, które nasłuchuje repliki podstawowej.

Na przykład masz ServiceRemotingListener, wykorzystującej wywołania RPC tylko w przypadku repliki podstawowej, a drugi, niestandardowe odbiornik, który przyjmuje odczytu żądania w replikach pomocniczych za pośrednictwem protokołu HTTP:

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
> Podczas tworzenia wielu odbiorników dla każdego odbiornika usługi **musi** należy nadać unikatową nazwę.
>
>

Ponadto opisano punkty końcowe, które są wymagane dla usługi w [manifestu usługi](service-fabric-application-and-service-manifests.md) sekcji w punktach końcowych.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Odbiornik komunikacji można uzyskiwać dostęp do zasobów punktu końcowego, przydzielone do niego z `CodePackageActivationContext` w `ServiceContext`. Odbiornik może następnie rozpocząć nasłuchiwanie żądań, gdy zostanie otwarta.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Zasoby punktu końcowego są wspólne dla pakietu całej usługi i są przydzielane przez usługę Service Fabric po aktywowaniu pakiet usługi. Wiele replik usługę hostowaną w tych samych ServiceHost może udostępniać tego samego portu. Oznacza to, że odbiornika komunikacji powinien obsługiwać współużytkowanie portów w. Jest to zalecany sposób to zrobić dla odbiornika komunikacji używania partycji, identyfikator i identyfikator repliki i wystąpienia, podczas generowania adresu nasłuchiwania.
>
>

### <a name="service-address-registration"></a>Rejestracja adres usługi
Usługa systemowa o nazwie *usługi nazewnictwa* działa w klastrach usługi Service Fabric. Usługa nazewnictwa jest rejestratora dla usług i ich adresy, które każdego wystąpienia lub repliki usługi nasłuchuje. Gdy `OpenAsync(C#) / openAsync(Java)` metody `ICommunicationListener(C#) / CommunicationListener(Java)` zakończeniu jego powrotu wartość zostaje zarejestrowany w usłudze nazewnictwa. Ta zwracanej wartości, która zostanie opublikowany w usłudze nazewnictwa jest ciąg, którego wartość może być dowolna wcale. Wartość ta będzie klientów widoczny podczas ich poprosić o adres usługi z usługi nazw.

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

Usługa Service Fabric udostępnia interfejsy API, dzięki czemu klienci i innych usług, następnie poproś dla tego adresu według nazwy usługi. Jest to ważne, ponieważ adres usługi nie jest statyczne. Usługi są przenoszone w klastrze do celów równoważenia i dostępność zasobów. Jest to mechanizm, który umożliwiają klientom rozpoznawanie adresu nasłuchiwania dla usługi.

> [!NOTE]
> Aby uzyskać pełne omówienie sposobu pisania odbiornika komunikacji, zobacz [usług internetowy interfejs API usługi Service Fabric przy użyciu własnym hostingu OWIN](service-fabric-reliable-services-communication-webapi.md) dla C#, natomiast dla języka Java można napisać własną implementację serwera HTTP, zobacz EchoServer Przykładowa aplikacja u https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Podczas komunikacji z usługą
Do interfejsu API Reliable Services zapewnia następujące biblioteki do zapisania klientów komunikujących się z usługami.

### <a name="service-endpoint-resolution"></a>Rozpoznanie punktu końcowego usługi
Pierwszym krokiem do komunikacji z usługą jest rozpoznania adresu punktu końcowego partycji lub wystąpienie usługi, który chcesz komunikować się. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` Klasa narzędzie jest podstawowe podstawowego, który pomaga klientom określić punkt końcowy usługi w czasie wykonywania. W terminologii usługi Service Fabric, czyli proces określania punktu końcowego usługi jest nazywany *rozpoznanie punktu końcowego usługi*.

Łączenie się z usługami w ramach klastra, ServicePartitionResolver mogą być tworzone przy użyciu ustawień domyślnych. Jest to zalecany sposób użycia w większości sytuacji:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Łączenie się z usługami w innym klastrze, ServicePartitionResolver można utworzyć przy użyciu zestawu punktów końcowych klastra bramy. Należy pamiętać, że punkty końcowe bramy są po prostu różnych punktów końcowych do łączenia się z tym samym klastrze. Na przykład:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternatywnie `ServicePartitionResolver` można podać funkcję tworzenia `FabricClient` do wewnętrznego użycia:

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

`FabricClient` jest obiekt, który jest używany do komunikacji z klastrem usługi Service Fabric dla różnych operacji zarządzania w klastrze. Jest to przydatne, gdy chcesz mieć większą kontrolę nad jak program rozpoznawania nazw partycji usługi współdziała z klastrem. `FabricClient` buforuje wewnętrznie i jest zazwyczaj kosztowne, dlatego ważne jest, aby ponownie użyć `FabricClient` wystąpień, ile to możliwe.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Metoda rozwiązania należy pobrać adres usługi lub partycji usługi dla usług podzielonym na partycje.

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

Adres usługi można rozwiązać za pomocą ServicePartitionResolver, ale więcej pracy jest wymagany w celu zapewnienia, że poprawnie można rozpoznać adresu. Twój klient musi wykryć, czy próba połączenia nie powiodło się z powodu błędu przejściowego i mogą być ponawiane (np. usługi przeniesiony lub jest tymczasowo niedostępna), lub z powodu trwałego błędu (np. usługa została usunięta lub żądany zasób nie istnieje). Wystąpienie usługi lub replik można przenieść z węzła do węzła w dowolnym momencie kilka przyczyn. Adres usługi rozwiązać za pomocą ServicePartitionResolver mogą być nieaktualne przez razem, gdy kod klienta próbuje nawiązać połączenie. W takim przypadku ponownie klient musi ponownie rozpoznawać adres. Zapewnianie poprzedniego `ResolvedServicePartition` oznacza, że program rozpoznawania nazw musi ponowienie próby zamiast po prostu pobrać adres pamięci podręcznej.

Zazwyczaj kod klienta muszą współdziała z ServicePartitionResolver bezpośrednio. Jest tworzony i przekazywane do komunikacji klienta fabryki niezawodnych usług interfejsu API. Fabryk generować za pomocą rozpoznawania nazw wewnętrznie obiektu klienta, który może służyć do komunikacji z usługami.

### <a name="communication-clients-and-factories"></a>Komunikacja klientów i fabryk
Biblioteka fabryki komunikacja implementuje typowy wzorzec ponawiania obsługi błędów, który ułatwia Trwa ponawianie próby połączenia z punktami końcowymi usługi rozwiązane. Biblioteka fabryki zapewnia mechanizm ponawiania prób, mimo że zapewnienia obsługi błędów.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` Określa podstawowy interfejs implementowany przez fabryka klientów komunikacji, który produkuje klientów, którzy mogą komunikować się z usługi Service Fabric. Implementacja CommunicationClientFactory zależy od stosu komunikacji używane przez usługę Service Fabric, w której klient chce się nawiązać połączenia. Zapewnia niezawodne interfejsu API usługi `CommunicationClientFactoryBase<TCommunicationClient>`. Udostępnia implementację podstawową interfejsu CommunicationClientFactory i wykonuje zadania, które są wspólne dla wszystkich stosów komunikacji. (Te zadania obejmują przy użyciu ServicePartitionResolver, aby określić punkt końcowy usługi). Klienci zwykle Implementowanie klasy abstrakcyjnej CommunicationClientFactoryBase do obsługi logiki, które są specyficzne dla stosu komunikacji.

Komunikacja klienta po prostu otrzymuje adres i używa ich do łączenia się z usługą. Klient może używać niezależnie od protokołu, należy utworzyć.

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

Fabryka klientów jest głównie odpowiedzialnych za tworzenie komunikacji klientów. Dla klientów, którzy nie zachować połączenie trwałe, takich jak klient HTTP fabryka jest tylko musi utworzyć i zwracać klienta. Inne protokoły, zapewniające połączenie trwałe, takie jak niektóre protokoły binarne, również powinny zostać zatwierdzone przez fabrykę, aby ustalić, czy połączenie musi zostać ponownie utworzone.  

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

Na koniec obsługi wyjątków jest odpowiedzialny za sprawdzenie, jaką akcję należy podjąć, gdy wystąpi wyjątek. Wyjątki są podzielone na kategorie **powtarzający operację** i **niepowtarzającego**.

* **Niepowtarzającego** wyjątki po prostu pobrać zgłaszany ponownie obiektu wywołującego.
* **powtarzający operację** wyjątki są dodatkowo podzielone na **przejściowy** i **nieprzejściowych**.
  * **Przejściowy** wyjątki są te, które mogą być ponawiane po prostu bez ponownego rozpoznawania adresu punktu końcowego usługi. Obejmują one co przejściowe problemy z siecią lub Usługa odpowiedzi na błędy niż te, które wskazują, że adres punktu końcowego usługi nie istnieje.
  * **Inne niż przejściowe** wyjątki są te, które wymagają adresu punktu końcowego usługi do można ponownie rozpoznać. Należą do nich wyjątki, które wskazują, że nie można osiągnąć punktu końcowego usługi, wskazujący usługę został przeniesiony do innego węzła.

`TryHandleException` Podejmuje decyzję o dany wyjątek. Jeśli go **nie zna** jakie decyzje o wyjątku, powinien zostać zwrócony **false**. Jeśli go **znać** jakie decyzja, należy odpowiednio ustawić wynik i zwraca **true**.

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
Za pomocą `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, i `IExceptionHandler(C#) / ExceptionHandler(Java)` korzystające z protokołu komunikacyjnego, `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` owija go wszystko ze sobą i zapewnia wokół tych składników pętlę rozpoznawania adresu partycji usługi i obsługi błędów.

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
* [Platforma ASP.NET Core przy użyciu usług Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Zdalne wywołania procedur z wywołaniem funkcji zdalnych usług Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Komunikacji WCF przy użyciu usług Reliable Services](service-fabric-reliable-services-communication-wcf.md)
