---
title: Zdalna komunikacja usług przy użyciu języka C# w usłudze Service Fabric | Dokumentacja firmy Microsoft
description: Komunikacja zdalna usługi Service Fabric umożliwia klientów i usług, do komunikowania się z usługami w języku C# za pomocą zdalnego wywołania procedury.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: f9cd6e2fee738d2d42c790b4eb7b9a876a44b01d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772979"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Zdalna komunikacja usług w języku C# przy użyciu usług Reliable Services

> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Dla usług, które nie są powiązane protokołu komunikacyjnego określonego lub stosem, na przykład internetowy interfejs API, programu Windows Communication Foundation lub innych usług Reliable Services w ramach udostępnia mechanizm komunikacji zdalnej szybkie i łatwe Konfigurowanie zdalnego wywołania procedury dla usługi. W tym artykule omówiono sposób konfigurowania zdalnego wywołania procedury usługi napisany w języku C#.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej usługi

Możesz skonfigurować komunikację zdalną dla usługi w dwa proste kroki:

1. Utwórz interfejs dla usługi w celu wdrożenia. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w Twojej usłudze. Metody muszą być zwracającą zadanie metod asynchronicznych. Interfejs musi implementować `Microsoft.ServiceFabric.Services.Remoting.IService` do sygnalizowania, że usługa ma interfejs komunikację zdalną.
2. Użyj odbiornika komunikacji zdalnej w usłudze. Odbiornik komunikacji zdalnej jest `ICommunicationListener` implementację, która oferuje możliwości komunikacji zdalnej. `Microsoft.ServiceFabric.Services.Remoting.Runtime` Przestrzeń nazw zawiera metodę rozszerzająca `CreateServiceRemotingListener` dla usług stanowych i bezstanowych, które mogą służyć do tworzenia odbiornika komunikacji zdalnej przy użyciu domyślnego protokołu transportu komunikacji zdalnej.

>[!NOTE]
>`Remoting` Przestrzeni nazw jest dostępna jako osobny pakiet NuGet o nazwie `Microsoft.ServiceFabric.Services.Remoting`.

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
> Argumenty i typy zwracane występujące w interfejsie usługi może być żadnych typów prostych, złożonych lub niestandardowego, ale muszą być w stanie przez .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Wywoływanie metod usługi zdalnej

Wywoływanie metod w usłudze przy użyciu stosu komunikacji zdalnej jest przeprowadzane za pomocą lokalnego serwera proxy do usługi za pośrednictwem `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasy. `ServiceProxy` Metoda tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługę. Za pomocą tego serwera proxy można wywołać metody w interfejsie zdalnie.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

W ramach komunikacji zdalnej propaguje wyjątki generowane przez usługę do klienta. W rezultacie, gdy `ServiceProxy`jest używany, klient jest odpowiedzialny za obsługę wyjątki generowane przez usługę.

## <a name="service-proxy-lifetime"></a>Okres istnienia usługi serwera proxy

Tworzenie serwera proxy usługi jest lekka operacja, aby można było utworzyć dowolną liczbę, według potrzeb. Wystąpienia serwera proxy usługi można ponownie tak długo, jak są one potrzebne. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można ponownie użyć tego samego wystąpienia serwera proxy. Każdy serwer proxy usługi zawiera komunikacji klienta używany do wysyłania wiadomości przez sieć. Podczas wywoływania wywołań zdalnych, wewnętrzne są sprawdzane w celu określenia, czy klient komunikacji jest nieprawidłowy. Na podstawie wyników tych kontroli, klienta komunikacji jest utworzone ponownie w razie potrzeby. W związku z tym, jeśli wystąpi wyjątek, nie trzeba ponownie utworzyć `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Okres istnienia fabryki serwera proxy usługi

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) fabryki, który tworzy wystąpienie serwera proxy dla różnych usług zdalnych interfejsów. Jeśli używasz interfejsu API `ServiceProxyFactory.CreateServiceProxy` do utworzenia serwera proxy, szablon tworzy serwer proxy usługi singleton.
Warto utworzyć ręcznie, gdy trzeba zastąpić [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) właściwości.
Tworzenie fabryki jest kosztowną operacją. Fabryka serwera proxy usług utrzymuje wewnętrzną pamięć podręczną klienta komunikacji.
Najlepszym rozwiązaniem jest w pamięci podręcznej fabryki serwera proxy usług tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Komunikacja zdalna obsługa wyjątków

Wszystkie wyjątki zdalnego zgłoszony przez interfejs API usługi są wysyłane do klienta jako aggregateexception —. Wyjątki zdalnego powinno być możliwe do zserializowania przez DataContract. Jeśli nie są one proxy interfejs API zgłasza [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) z powodu błędu serializacji w nim.

Serwer proxy usługi obsługiwać wszystkie wyjątki trybu failover dla partycji usługi jest tworzona dla. On ponownie jest rozpoznawana jako punktów końcowych w przypadku pracy awaryjnej wyjątków (inne niż przejściowe wyjątków) i ponawia próbę wywołania z właściwego punktu końcowego. Liczba ponownych prób dla trybu failover wyjątków jest nieokreślony.
Jeśli wystąpią przejściowych wyjątków, serwer proxy ponawia próbę wywołania.

Domyślne parametry ponawiania prób są dostarczane przez [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Te wartości można skonfigurować przez przekazanie obiektu OperationRetrySettings do konstruktora ServiceProxyFactory użytkownika.

## <a name="use-the-remoting-v2-stack"></a>Korzystanie z stosu komunikacji zdalnej w wersji 2

Począwszy od wersji 2.8 pakietu NuGet usług zdalnych masz możliwość korzystania ze stosu V2 komunikacji zdalnej. Stos komunikacji zdalnej V2 działa lepiej. Udostępnia również funkcje, takie jak niestandardowej serializacji i bardziej podłączanych interfejsów API.
Kod szablonu w dalszym ciągu używa stosu V1 komunikacji zdalnej.
Wersji 2 zdalnej komunikacji nie jest zgodny z V1 (poprzedniej stosu komunikacji zdalnej). Postępuj zgodnie z instrukcjami w artykule [uaktualnienie z V1 na V2](#upgrade-from-remoting-v1-to-remoting-v2) Aby uniknąć wpływu na dostępność usług.

Poniższych metod są dostępne, aby umożliwić stack w wersji 2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Użyj atrybutu zestawu do korzystania ze stosu w wersji 2

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

3. Oznacz zestaw, który zawiera interfejsy komunikacji zdalnej z `FabricTransportServiceRemotingProvider` atrybutu.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Bez zmian w kodzie są wymagane w projekcie klienta.
Tworzenie zestawów klienta z zestawem interfejsu, aby upewnić się, że atrybutu zestawu z wcześniejszymi jest używany.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Używanie jawnych klas w wersji 2 do korzystania ze stosu w wersji 2

Jako alternatywę do przy użyciu atrybutu zestawu stack w wersji 2 również można włączyć za pomocą jawnego klasy V2.

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

3. Użyj [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` przestrzeni nazw, aby utworzyć klientów.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Uaktualnienie z wywołaniem funkcji zdalnych V1 do komunikacji zdalnej w wersji 2

Aby uaktualnić z V1 na V2, dwuetapowej uaktualnień są wymagane. Wykonaj kroki opisane w tej sekwencji.

1. Uaktualnij usługę V1 do usługi w wersji 2, za pomocą tego atrybutu.
Ta zmiana gwarantuje, że usługa nasłuchuje na odbiornik V1 i V2.

    a. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Użyj następującej metody rozszerzenia do utworzenia odbiornika komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Dodawanie atrybutu zestawu w interfejsach remoting do użycia klient w wersji 2 i odbiornika V1 i V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Uaktualnienie klienta V1 do klienta w wersji 2 przy użyciu atrybutu klienta w wersji 2.
Ten krok zapewnia, że klient używa stosu w wersji 2.
Nie wprowadzono zmian w projekcie/Usługa klienta jest wymagany. Kompilowanie projektów klienckich przy użyciu zestawu zaktualizowany interfejs jest wystarczająca.

3. Ten krok jest opcjonalny. Użyj atrybutu odbiornika V2, a następnie Uaktualnij usługę w wersji 2.
Ten krok pozwala się upewnić, że usługa nasłuchuje tylko odbiornika V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Korzystanie z komunikacji zdalnej V2 stosu (zgodny z interfejsu)

 Komunikacja zdalna V2 (interfejs zgodne, znane jako V2_1) stos zawiera wszystkie funkcje w wersji 2 stosu komunikacji zdalnej. Stos interfejsu jest zgodna z komunikacji zdalnej stack w wersji 1, ale nie jest zgodne z poprzednimi wersjami z wersji 2 i w wersji 1. Aby uaktualnić V1 do V2_1 bez wywierania wpływu na dostępność usługi, postępuj zgodnie z instrukcjami w artykule uaktualnienia z wersji 1 do wersji 2 (interfejs zgodne).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Użyj atrybutu zestawu do korzystania ze stosu (zgodny z interfejsem) remoting V2

Wykonaj następujące kroki, aby zmienić stos V2_1.

1. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Metoda rozszerzenia usług zdalnych umożliwia utworzenie odbiornika komunikacji zdalnej.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Dodaj [atrybutu zestawu](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) na interfejsach komunikacji zdalnej.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Żadne zmiany nie są wymagane w projekcie klienta.
Tworzenie zestawów klienta z zestawem interfejsu, aby upewnić się, że poprzednie atrybutu zestawu jest używana.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Używanie klas jawne remoting do utworzenia fabryki odbiornika/kliencie systemu Windows dla wersji (zgodny z interfejsem) w wersji 2

Wykonaj następujące kroki:

1. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Użyj [odbiornika komunikacji zdalnej V2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Nazwa zasobu punktu końcowego usługi domyślne używane jest "ServiceEndpointV2_1." Musi być zdefiniowany w manifeście usługi.

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

3. Należy użyć V2 [fabryka klientów](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Uaktualnienie z wywołaniem funkcji zdalnych V1 do komunikacji zdalnej w wersji 2 (interfejs niezgodny)

Aby uaktualnić z V1 na V2 (interfejs zgodne, znane jako V2_1), dwuetapowej uaktualnień są wymagane. Wykonaj kroki opisane w tej sekwencji.

1. Uaktualnij usługę V1 V2_1 usługi, korzystając z następującego atrybutu.
Ta zmiana sprawdza, czy usługa nasłuchuje na V1 i odbiornika V2_1.

    a. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Użyj następującej metody rozszerzenia do utworzenia odbiornika komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Dodaj atrybut zestawu w interfejsach komunikacji zdalnej do używania V1, odbiornik V2_1 i V2_1 klienta.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Uaktualnienie klienta V1 do klienta V2_1 za pomocą atrybutu V2_1 klienta.
Ten krok pozwala się upewnić, że klient używa stosu V2_1.
Nie wprowadzono zmian w projekcie/Usługa klienta jest wymagany. Kompilowanie projektów klienckich przy użyciu zestawu zaktualizowany interfejs jest wystarczająca.

3. Ten krok jest opcjonalny. Usuń wersję odbiornika V1 z atrybutu, a następnie Uaktualnij usługę w wersji 2.
Ten krok pozwala się upewnić, że usługa nasłuchuje tylko odbiornika V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Użyj niestandardowej serializacji komunikatem opakowanej komunikacji zdalnej

Wiadomości opakowana komunikacji zdalnej możemy utworzyć pojedynczy obiekt opakowany ze wszystkimi parametrami jako pole w nim.
Wykonaj następujące kroki:

1. Implementowanie `IServiceRemotingMessageSerializationProvider` interfejs do implementacji do szeregowania niestandardowego.
    Ten fragment kodu pokazuje, jak wygląda implementacja.

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

2. Przesłonić domyślny dostawca serializacji z `JsonSerializationProvider` dla odbiornika komunikacji zdalnej.

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

3. Przesłonić domyślny dostawca serializacji z `JsonSerializationProvider` fabryki klienta komunikacji zdalnej.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Kolejne kroki

* [Internetowy interfejs API z oprogramowaniem OWIN usług Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation komunikacji przy użyciu usług Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Bezpieczna komunikacja dla usług Reliable Services](service-fabric-reliable-services-secure-communication.md)
