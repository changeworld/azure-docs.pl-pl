---
title: Usługa zdalnych w sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Sieć szkieletowa usług zdalnych umożliwia klientów i usług do komunikowania się z usługami za pomocą zdalnego wywołania procedury.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: d9ba650549d313a4ecc9ceae5eb05e1cde727892
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="service-remoting-with-reliable-services"></a>Komunikacji zdalnej usługi z usługami Reliable Services
Dla usług, które nie są związane z protokołu komunikacyjnego konkretnego lub stosu, takie jak WebAPI, Windows Communication Foundation (WCF) lub innych osób, w ramach niezawodnej usługi udostępnia mechanizm komunikacji zdalnej szybkie i łatwe Konfigurowanie zdalnego wywołania procedury dla usługi.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej usługi
Konfigurowanie komunikacji zdalnej usługi odbywa się w dwóch prostych krokach:

1. Utwórz interfejs do implementacji usługi. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w usłudze. Metody muszą być zwracanie zadań metod asynchronicznych. Musi implementować interfejs `Microsoft.ServiceFabric.Services.Remoting.IService` sygnalizują, że usługa ma interfejs usług zdalnych.
2. Użyj odbiornika usługi zdalne w usłudze. Jest RemotingListener `ICommunicationListener` implementację, która zapewnia możliwości komunikacji zdalnej. `Microsoft.ServiceFabric.Services.Remoting.Runtime` Przestrzeń nazw zawiera metody rozszerzenia`CreateServiceRemotingListener` dla usług zarówno bezstanowe i stanowe, które mogą służyć do tworzenia odbiornik komunikacji zdalnej przy użyciu protokołu transportu domyślnego komunikacji zdalnej.

>[!NOTE]
>`Remoting` Przestrzeń nazw jest dostępna jako osobny pakiet NuGet o nazwie `Microsoft.ServiceFabric.Services.Remoting`

Na przykład następującej usługi bezstanowej przedstawia jedną metodę można uzyskać za pośrednictwem zdalnego wywołania procedury "Hello World".

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Argumenty i typy zwracane w interfejsie usługi może być żadnych typów prosty, złożonych lub niestandardowy, ale musi być serializację za pomocą programu .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Wywołanie metody zdalnej usługi
Wywołanie metod w usłudze przy użyciu stosu komunikacji zdalnej odbywa się za pomocą lokalnego serwera proxy do usługi za pośrednictwem `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasy. `ServiceProxy` Metoda tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługę. Z tego serwera proxy można wywołać metody w interfejsie zdalnie.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

W ramach usług zdalnych propaguje wyjątki zgłaszane przez usługę do klienta. W rezultacie, korzystając z `ServiceProxy`, klient jest odpowiedzialny za obsługę wyjątków zgłaszanych przez usługę.

## <a name="service-proxy-lifetime"></a>Okres istnienia usługi serwera Proxy
Tworzenie ServiceProxy jest lekkie operacji, aby użytkownicy mogli tworzyć dowolną liczbę potrzebnych im. Tak długo, jak użytkownicy muszą ją można ponownie użyć wystąpienia serwera Proxy usługi. Jeśli zdalne wywołanie procedury zgłasza wyjątek, użytkownicy mogą nadal ponownie użyć tego samego wystąpienia serwera proxy. Każdy ServiceProxy zawiera komunikacji klienta używany do wysyłania wiadomości przez sieć. Podczas wywoływania wywołań zdalnych, firma Microsoft wewnętrznie Sprawdź, czy klient komunikacji jest prawidłowy. Na podstawie tego wyniku, możemy ponownie utworzyć klienta komunikacji w razie potrzeby. Dlatego jeśli wystąpi wyjątek, użytkownicy nie muszą utworzyć ponownie `ServiceProxy` ponieważ go to zrobione przezroczysty.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) jest fabrykę tworzącą wystąpień serwera proxy dla różnych usług zdalnych interfejsów. Jeśli używasz interfejsu api `ServiceProxy.Create` tworzenia serwera proxy, framework utworzy pojedynczą ServiceProxy.
Warto utworzyć jedną ręcznie, gdy trzeba zastąpić [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) właściwości.
Tworzenie fabryki jest kosztowna operacja. ServiceProxyFactory przechowuje wewnętrznej pamięci podręcznej klienta komunikacji.
Najlepszym rozwiązaniem jest pamięci podręcznej ServiceProxyFactory tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Obsługa wyjątków komunikacji zdalnej
Wszystkie wyjątki zdalnego zgłoszony przez interfejs API usługi są wysyłane do klienta jako AggregateException. RemoteExceptions powinien być możliwy do serializacji; DataContract Jeśli nie są one proxy interfejsu API zgłasza [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) z powodu błędu serializacji w nim.

ServiceProxy obsługiwać wszystkie wyjątki trybu failover jest tworzony dla partycji usługi. Go ponownie usuwa punkty końcowe, jeśli istnieją wyjątki trybu failover (z systemem innym niż przejściowy wyjątkami) i ponowi próbę połączenia z właściwego punktu końcowego. Liczba ponownych prób dla trybu failover wyjątki są nieokreślony.
Jeśli wystąpią wyjątki przejściowy, serwera proxy ponowi próbę połączenia.

Domyślne parametry ponawiania są zachowywane przez [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Użytkownik może skonfigurować te wartości przez przekazanie obiektu OperationRetrySettings ServiceProxyFactory konstruktora.
## <a name="how-to-use-remoting-v2-stack"></a>Jak używać usług zdalnych V2 stosu
Przy użyciu pakietu NuGet usługi zdalne 2,8 masz możliwość użycia stosu V2 komunikacji zdalnej. Stos V2 komunikacji zdalnej jest więcej wydajności i zapewnia bardziej podłączany interfejsów Api i funkcje, takie jak niestandardowe, które można serializować.
Domyślnie jeśli nie ma następujących zmian, go będzie później nadal używać stosu V1 komunikacji zdalnej.
V2 usług zdalnych nie jest zgodny z V1 (poprzednie stosu komunikacji zdalnej), wykonaj tak poniżej artykuł na temat uaktualniania z wersji 1 do V2 bez wpływu na dostępność usługi.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Użyj stosu V2 przy użyciu atrybutu zestawu.

Poniżej przedstawiono kroki, aby wykonać, aby zmienić stos V2.

1. Dodaj zasób punktu końcowego o nazwie jako "ServiceEndpointV2" w manifeście usługi.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Umożliwia utworzenie odbiornika usługi zdalne — metoda rozszerzenia usług zdalnych.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Dodaj atrybut zestawu w interfejsach komunikacji zdalnej.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
W projekcie klienta nie są konieczne nie zmiany.
Tworzenie zestawu klienta z interfejsu zestawu, do upewnia się, że powyżej zestaw atrybutów jest używana.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Tworzenie odbiornika za pomocą jawnej klasy V2 / ClientFactory
Poniżej przedstawiono kroki do wykonania.
1.  Dodaj zasób punktu końcowego o nazwie jako "ServiceEndpointV2" w manifeście usługi.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Użyj [Remoting V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet). Domyślna nazwa zasobu punktu końcowego usługi używany jest "ServiceEndpointV2" i musi być zdefiniowana w elemencie Service Manifest.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Użyj V2 [fabryka klientów](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Jak uaktualnić od Remoting V1 do komunikacji zdalnej V2.
Aby przeprowadzić uaktualnienie z wersji 1 V2, wymagane są uaktualnienia krok 2. Poniższe kroki, aby wykonać w sekwencji na liście.

1. Uaktualnij usługę V1 usługą V2 przy użyciu atrybutu CompactListener.
Ta zmiana upewnia się, że usługa nasłuchuje V1 i V2 odbiornika.

    ) Dodaj zasób punktu końcowego o nazwie jako "ServiceEndpointV2" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    (b) Użyj poniższego metodę rozszerzenie w celu utworzenia odbiornika usługi zdalne.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Dodaj atrybut zestawu na interfejsach usług zdalnych, użyj CompatListener i klienta w wersji 2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Uaktualnienie klienta V1 V2 klienta przy użyciu atrybutu klienta V2.
Ten krok zapewnia, że klient używa stosu V2.
Brak zmian w projekcie/Usługa klienta jest wymagana. Kompilowanie projektów klienckich z zestawu zaktualizowany interfejs jest wystarczająca.

3. Ten krok jest opcjonalny. Użyj atrybutu V2Listener, a następnie Uaktualnij usługę V2.
Ten krok upewnia się, czy usługa nasłuchuje tylko odbiornika V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Jak używać niestandardowej serializacji z V2 komunikacji zdalnej.
Poniższy przykład używa serializacji Json z V2 komunikacji zdalnej.
1. Zaimplementuj interfejs IServiceRemotingMessageSerializationProvider do implementacji niestandardowej serializacji.
    Oto fragment kodu w sposób wykonania wygląda następująco.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    Zastąp domyślny dostawca serializacji z JsonSerializationProvider dla odbiornika usługi zdalne.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Domyślny dostawca serializacji z JsonSerializationProvider zastąpienia fabryka klientów zdalnych.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Kolejne kroki
* [Interfejs API OWIN w niezawodnej usługi sieci Web](service-fabric-reliable-services-communication-webapi.md)
* [Komunikacyjny WCF z usługami Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Zabezpieczenia komunikacji niezawodnej usług](service-fabric-reliable-services-secure-communication.md)
