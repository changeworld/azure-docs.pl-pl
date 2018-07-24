---
title: Zdalna komunikacja usług przy użyciu języka C# w usłudze Service Fabric | Dokumentacja firmy Microsoft
description: Komunikacja zdalna usługi Service Fabric umożliwia klientów i usług, do komunikowania się z usługami w języku C# za pomocą zdalnego wywołania procedury.
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
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205820"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Zdalna komunikacja usług w języku C# przy użyciu usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Dla usług, które nie są powiązane protokołu komunikacyjnego określonego lub stos, takie jak WebAPI, Windows Communication Foundation (WCF) lub innych usług Reliable Services w ramach udostępnia mechanizm komunikacji zdalnej szybkie i łatwe Konfigurowanie zdalnego wywołania procedury dla usługi. W tym artykule omówiono sposób konfigurowania zdalnego wywołania procedury usługi napisany w języku C#.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej usługi
Konfigurowanie komunikacji zdalnej usługi odbywa się w dwa proste kroki:

1. Utwórz interfejs dla usługi w celu wdrożenia. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w Twojej usłudze. Metody muszą być zwracającą zadanie metod asynchronicznych. Interfejs musi implementować `Microsoft.ServiceFabric.Services.Remoting.IService` do sygnalizowania, że usługa ma interfejs komunikację zdalną.
2. Użyj odbiornika komunikacji zdalnej w usłudze. Jest RemotingListener `ICommunicationListener` implementację, która oferuje możliwości komunikacji zdalnej. `Microsoft.ServiceFabric.Services.Remoting.Runtime` Przestrzeń nazw zawiera metodę rozszerzającą`CreateServiceRemotingListener` dla usług stanowych i bezstanowych, które mogą służyć do tworzenia odbiornika komunikacji zdalnej przy użyciu domyślnego protokołu transportu komunikacji zdalnej.

>[!NOTE]
>`Remoting` Przestrzeni nazw jest dostępna jako osobny pakiet NuGet o nazwie `Microsoft.ServiceFabric.Services.Remoting`

Na przykład następujące usługi bezstanowej udostępnia pojedynczą metodę można pobrać "Hello World" za pośrednictwem zdalnego wywołania procedury.

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

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```
> [!NOTE]
> Argumenty i typy zwracane występujące w interfejsie usługi może być żadnych typów prostych, złożonych lub niestandardowego, ale musi być możliwy do serializacji, .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Wywoływanie metod usługi zdalnej
Wywoływanie metod w usłudze przy użyciu stosu komunikacji zdalnej jest przeprowadzane za pomocą lokalnego serwera proxy do usługi za pośrednictwem `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasy. `ServiceProxy` Metoda tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługę. Za pomocą tego serwera proxy można wywołać metody w interfejsie zdalnie.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

W ramach komunikacji zdalnej propaguje wyjątki generowane przez usługę do klienta. W rezultacie, korzystając z `ServiceProxy`, klient jest odpowiedzialny za obsługę wyjątki generowane przez usługę.

## <a name="service-proxy-lifetime"></a>Okres istnienia usługi serwera Proxy
Tworzenie ServiceProxy jest lekka operacja, aby można było utworzyć dowolną liczbę, według potrzeb. Tak długo, jak są one potrzebne, mogą zostać ponownie użyte wystąpień usługi Serwer Proxy. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można ponownie użyć tego samego wystąpienia serwera proxy. Każdy ServiceProxy zawiera komunikacji klienta używany do wysyłania wiadomości przez sieć. Podczas wywoływania wywołań zdalnych, wewnętrzne są sprawdzane w celu określenia, czy klient komunikacji jest nieprawidłowy. Na podstawie wyników tych kontroli, utworzone ponownie klienta komunikacji, jeśli to konieczne. W związku z tym, jeśli wystąpi wyjątek, nie trzeba ponownie utworzyć `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) fabryki, który tworzy wystąpienie serwera proxy dla różnych usług zdalnych interfejsów. Jeśli używasz interfejsu api `ServiceProxy.Create` następnie do tworzenia serwera proxy, szablon tworzy pojedyncze ServiceProxy.
Warto utworzyć ręcznie, gdy trzeba zastąpić [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) właściwości.
Tworzenie fabryki jest kosztowną operacją. ServiceProxyFactory utrzymuje wewnętrzną pamięć podręczną klienta komunikacji.
Najlepszym rozwiązaniem jest buforowanie ServiceProxyFactory tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Komunikacja zdalna obsługa wyjątków
Wszystkie wyjątki zdalnego zgłoszony przez interfejs API usługi są wysyłane do klienta jako aggregateexception —. RemoteExceptions powinien być możliwy do serializacji; DataContract Jeśli nie są one proxy interfejs API zgłasza [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) z powodu błędu serializacji w nim.

ServiceProxy obsługiwać wszystkie wyjątki trybu failover dla partycji usługi jest tworzona dla. On ponownie jest rozpoznawana jako punktów końcowych w przypadku pracy awaryjnej wyjątków (inne niż przejściowe wyjątków) i ponawia próbę wywołania z właściwego punktu końcowego. Liczba ponownych prób dla trybu failover wyjątki są nieokreślony.
Jeśli wystąpią przejściowych wyjątków, serwer proxy ponawia próbę wywołania.

Domyślne parametry ponawiania prób są dostarczane przez [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Użytkownik może skonfigurować te wartości przez przekazanie obiektu OperationRetrySettings ServiceProxyFactory konstruktora.

## <a name="how-to-use-the-remoting-v2-stack"></a>Sposób korzystania ze stosu komunikacji zdalnej w wersji 2

Począwszy od wersji pakietu NuGet usług zdalnych 2.8 masz możliwość korzystania ze stosu w wersji 2 zdalnej komunikacji. Stos komunikacji zdalnej w wersji 2 jest wydajniej i udostępnia funkcje, takie jak niestandardowej serializacji i bardziej podłączanych interfejsu API.
Kod szablonu w dalszym ciągu używa stosu V1 komunikacji zdalnej.
Wersji 2 zdalnej komunikacji nie jest zgodny z V1 (poprzedniej stosu komunikacji zdalnej), więc postępuj zgodnie z instrukcjami poniżej na [sposób uaktualnienia programu V1 na V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) bez wywierania wpływu na dostępność usług.

Poniższych metod są dostępne, aby umożliwić stack w wersji 2.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Przy użyciu atrybutu zestawu do korzystania ze stosu w wersji 2

Następujące kroki, Zmień kod szablonu do korzystania ze stosu w wersji 2 przy użyciu atrybutu zestawu.

1. Zmień zasób punktu końcowego z `"ServiceEndpoint"` do `"ServiceEndpointV2"` w manifeście usługi.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Użyj `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` metodę rozszerzenia, aby utworzyć odbiorników komunikacji zdalnej (równe zarówno V1 i V2).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Oznacz zestaw zawierający interfejsy komunikacji zdalnej z `FabricTransportServiceRemotingProvider` atrybutu.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
  ```

Bez zmian w kodzie są wymagane w projekcie klienta.
Tworzenie zestawów klienta z zestawem interfejsu, aby upewnić się, że atrybutu zestawu powyżej jest używany.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Używanie jawnych klas V2 do korzystania ze stosu w wersji 2

Jako alternatywę do przy użyciu atrybutu zestawu stack w wersji 2 można również włączyć przy użyciu jawne klas w wersji 2.

Następujące kroki, Zmień kod szablonu do korzystania ze stosu w wersji 2 przy użyciu jawne klas w wersji 2.

1. Zmień zasób punktu końcowego z `"ServiceEndpoint"` do `"ServiceEndpointV2"` w manifeście usługi.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Użyj [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` przestrzeni nazw.

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

3. Użyj [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` przestrzeni nazw, aby utworzyć klientów.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Jak uaktualnić do wersji 2 zdalnej komunikacji V1 komunikacji zdalnej.
Aby uaktualnić z V1 na V2, wymagane są uaktualnienia w kroku 2. Poniższe kroki, aby zostać wykonane w sekwencji na liście.

1. Uaktualnij usługę V1 do usługi w wersji 2, za pomocą poniżej atrybutu.
Ta zmiana sprawdza, czy usługa nasłuchuje na V1 i V2 odbiornika.

    ) Dodaj zasób punktu końcowego o nazwie jako "ServiceEndpointV2" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    (b) Użyj następującej metody rozszerzenia, aby utworzyć odbiornik komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Dodawanie atrybutu zestawu na interfejsach komunikację zdalną, użyj V1 i V2 odbiornika oraz klient w wersji 2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Uaktualnienie klienta V1 do klienta w wersji 2 przy użyciu atrybutu klienta w wersji 2.
Ten krok pozwala się upewnić, że klient używa stosu w wersji 2.
Nie wprowadzono zmian w projekcie/Usługa klienta jest wymagany. Kompilowanie projektów klienckich przy użyciu zestawu zaktualizowany interfejs jest wystarczająca.

3. Ten krok jest opcjonalny. Użyj atrybutu V2Listener, a następnie Uaktualnij usługę w wersji 2.
Ten krok zapewnia, że usługa słucha tylko odbiornik V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>Jak używać komunikacji zdalnej V2(InterfaceCompatible) stosu
 Wersji 2 zdalnej komunikacji stosu (InterfaceCompatible alias V2_1) zawiera wszystkie funkcje usług zdalnych V2 stosu oprócz interfejsu zgodne stosu do stosu komunikacji zdalnej w wersji 1, ale nie jest zgodny z w wersji 2 i w wersji 1. Aby wykonać uaktualnienie z wersji 1 V2_1 bez wywierania wpływu na dostępność usługi, wykonaj poniższe artykułu[sposobu uaktualniania z wersji 1 do V2(InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible).


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>Korzystanie z komunikacji zdalnej V2(InterfaceCompatible) stosu przy użyciu atrybutu zestawu.

Poniżej przedstawiono kroki, które trzeba wykonać, aby zmienić V2_1 stosu.

1. Dodaj zasób punktu końcowego o nazwie jako "ServiceEndpointV2_1" w manifeście usługi.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2.  Użyj komunikacji zdalnej metody rozszerzenia, aby utworzyć odbiornik komunikacji zdalnej.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Dodaj [atrybutu zestawu](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) na interfejsach komunikacji zdalnej.

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
Żadne zmiany nie są wymagane w projekcie klienta.
Kompilacja zestawów klienta z zestawem interfejsu, aby upewnia się, że powyżej zestawu atrybutów jest używana.

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>Przy użyciu jawne klas usług zdalnych do utworzenia odbiornika / ClientFactory V2(InterfaceCompatible) wersji.
Poniżej przedstawiono kroki do wykonania.
1.  Dodaj zasób punktu końcowego o nazwie jako "ServiceEndpointV2_1" w manifeście usługi.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. Użyj [Remoting V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Domyślna nazwa zasobu punktu końcowego usługi, używana jest "ServiceEndpointV2_1" i musi być zdefiniowany w manifeście usługi.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. Użyj V2 [fabryka klientów](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>Jak uaktualnić z komunikacji zdalnej w wersji 1 do V2(InterfaceCompatible) komunikacji zdalnej.
W celu uaktualnienia z V1 na V2 (InterfaceCompatible alias V2_1), wymagane są uaktualnienia w kroku 2. Poniższe kroki, aby zostać wykonane w sekwencji na liście.

1. Uaktualnij usługę w wersji 1 usługi V2_1 przy użyciu następującego atrybutu.
Ta zmiana sprawdza, czy usługa nasłuchuje na V1 i V2_1 odbiornika.

    ) Dodaj zasób punktu końcowego o nazwie jako "ServiceEndpointV2_1" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    (b) Użyj następującej metody rozszerzenia, aby utworzyć odbiornik komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Dodawanie atrybutu zestawu w interfejsach komunikacji zdalnej w wersji 1, odbiornik V2_1 i V2_1 klienta.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Uaktualnienie klienta V1 do V2_1 klienta za pomocą atrybutu klienta V2_1.
Ten krok pozwala się upewnić, że klient używa stosu V2_1.
Nie wprowadzono zmian w projekcie/Usługa klienta jest wymagany. Kompilowanie projektów klienckich przy użyciu zestawu zaktualizowany interfejs jest wystarczająca.

3. Ten krok jest opcjonalny. Usuń wersję odbiornika V1 z atrybutu, a następnie Uaktualnij usługę w wersji 2.
Ten krok zapewnia, że usługa słucha tylko odbiornik V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>Jak używać niestandardowego serializacji komunikatem opakowane komunikacji zdalnej.
W komunikacie opakowane Remoting możemy utworzyć pojedynczy obiekt opakowany ze wszystkimi parametrami jako pole w nim.
Poniżej przedstawiono kroki:

1. Implementuj interfejs IServiceRemotingMessageSerializationProvider do implementacji do szeregowania niestandardowego.
    Poniżej przedstawiono fragment kodu jak wygląda implementacja.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2.    Zastąp domyślny dostawca serializacji z JsonSerializationProvider dla odbiornika komunikacji zdalnej.

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

3.    Zastąp domyślny dostawca serializacji z JsonSerializationProvider dla fabryka klientów zdalnych.

```csharp
var proxyFactory = new ServiceProxyFactory((c) =>
{
    return new FabricTransportServiceRemotingClientFactory(
    serializationProvider: new ServiceRemotingJsonSerializationProvider());
  });
  ```
## <a name="next-steps"></a>Kolejne kroki
* [Internetowy interfejs API z oprogramowaniem OWIN usług Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Komunikacji WCF usług Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Zabezpieczenia komunikacji w przypadku usług Reliable Services](service-fabric-reliable-services-secure-communication.md)
