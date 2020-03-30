---
title: Komunikacji zdalnej usługi przy użyciu języka C# w sieci szkieletowej usług
description: Komunikacji zdalnej sieci szkieletowej usług umożliwia klientom i usługom komunikowanie się z usługami Języka C# przy użyciu zdalnego wywołania procedury.
author: vturecek
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 0d59275f25931a11b2d551a2e9eb019838e4c1b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433885"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Komunikacji zdalnej usługi w języku C# z niezawodne usługi

> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

W przypadku usług, które nie są powiązane z określonym protokołem komunikacyjnym lub stosem, takich jak internetowy interfejs API, Windows Communication Foundation lub inne, struktura niezawodnych usług zapewnia mechanizm komunikacji zdalnej umożliwiający szybkie i łatwe konfigurowanie wywołań procedur zdalnych Usług. W tym artykule omówiono sposób konfigurowania zdalnych wywołań procedur dla usług napisanych w języku C#.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej w usłudze

Usługę można skonfigurować w dwóch prostych krokach:

1. Utwórz interfejs dla usługi do zaimplementowania. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w usłudze. Metody muszą być zwracające zadania metody asynchroniczne. Interfejs musi `Microsoft.ServiceFabric.Services.Remoting.IService` zostać zaimplementował, aby zasygnalizować, że usługa ma interfejs komunikacji zdalnej.
2. Użyj odbiornika komunikacji zdalnej w usłudze. Odbiornik komunikacji zdalnej jest `ICommunicationListener` implementacją, która zapewnia możliwości komunikacji zdalnej. Obszar `Microsoft.ServiceFabric.Services.Remoting.Runtime` nazw zawiera metodę `CreateServiceRemotingInstanceListeners` rozszerzenia dla usług bezstanowych i stanowych, które mogą służyć do tworzenia odbiornika komunikacji zdalnej przy użyciu domyślnego protokołu transportu komunikacji zdalnej.

>[!NOTE]
>Obszar `Remoting` nazw jest dostępny jako oddzielny `Microsoft.ServiceFabric.Services.Remoting`pakiet NuGet o nazwie .

Na przykład następująca usługa bezstanowa udostępnia pojedynczą metodę, aby uzyskać "Hello World" za pomocą zdalnego wywołania procedury.

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
> Argumenty i typy zwracane w interfejsie usługi mogą być typami prostymi, złożonymi lub niestandardowymi, ale muszą być w stanie być serializowane przez program .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Wywoływanie zdalnych metod serwisu

Wywoływanie metod w usłudze przy użyciu stosu komunikacji zdalnej odbywa `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` się przy użyciu lokalnego serwera proxy do usługi za pośrednictwem klasy. Metoda `ServiceProxy` tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługa. Za pomocą tego serwera proxy można zdalnie wywoływać metody w interfejsie.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Struktura komunikacji zdalnej propaguje wyjątki zgłaszane przez usługę do klienta. W rezultacie, `ServiceProxy`gdy jest używany, klient jest odpowiedzialny za obsługę wyjątków zgłaszanych przez usługę.

## <a name="service-proxy-lifetime"></a>Okres istnienia serwera proxy usługi

Tworzenie serwera proxy usługi jest lekką operacją, dzięki czemu można utworzyć dowolną liczbę. Wystąpienia serwera proxy usługi mogą być ponownie używane tak długo, jak są potrzebne. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można ponownie użyć tego samego wystąpienia serwera proxy. Każdy serwer proxy usługi zawiera klienta komunikacji używane do wysyłania wiadomości za połacie. Podczas wywoływania wywołań zdalnych wykonywane są kontrole wewnętrzne w celu ustalenia, czy klient komunikacji jest prawidłowy. Na podstawie wyników tych kontroli klient komunikacji jest ponownie tworzony w razie potrzeby. W związku z tym w przypadku wystąpienia wyjątku `ServiceProxy`nie trzeba ponownie utworzyć .

### <a name="service-proxy-factory-lifetime"></a>Okres istnienia fabryki serwera proxy usługi

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) to fabryka, która tworzy wystąpienia serwera proxy dla różnych interfejsów komunikacji zdalnej. Jeśli używasz `ServiceProxyFactory.CreateServiceProxy` interfejsu API do utworzenia serwera proxy, struktura tworzy serwer proxy usługi singleton.
Jest to przydatne do utworzenia ręcznie, gdy trzeba zastąpić [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) właściwości.
Tworzenie fabryki jest kosztowną operacją. Fabryka serwera proxy usługi utrzymuje wewnętrzną pamięć podręczną klienta komunikacji.
Najlepszym rozwiązaniem jest buforowanie fabryki serwera proxy usługi tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Obsługa wyjątków komunikacji zdalnej

Wszystkie wyjątki zdalne generowane przez interfejs API usługi są wysyłane z powrotem do klienta jako AggregateException. Wyjątki zdalne powinny mieć możliwość serializacji przez DataContract. Jeśli tak nie jest, interfejs API serwera proxy zgłasza [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) z błędem serializacji w nim.

Serwer proxy usługi obsługuje wszystkie wyjątki trybu failover dla partycji usługi, dla jakiej jest utworzona. Ponownie rozpoznaje punkty końcowe, jeśli istnieją wyjątki trybu failover (wyjątki nieprzejściowe) i ponawia ponawia wywołanie z poprawnym punktem końcowym. Liczba ponownych prób dla wyjątków trybu failover jest nieokreślony.
Jeśli występują przejściowe wyjątki, serwer proxy ponawia ponawia wywołanie.

Domyślne parametry ponawiania próby są dostarczane przez [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Użytkownik może skonfigurować te wartości, przekazując OperationRetrySettings obiektu do ServiceProxyFactory konstruktora.

## <a name="use-the-remoting-v2-stack"></a>Korzystanie ze stosu komunikacji zdalnej V2

W wersji 2.8 pakietu komunikacji zdalnej NuGet, masz możliwość korzystania z komunikacji zdalnej V2 stosu. Stos komunikacji zdalnej V2 działa lepiej. Zapewnia również funkcje, takie jak niestandardowa serializacja i bardziej podłączane interfejsy API.
Kod szablonu nadal używa stosu komunikacji zdalnej V1.
Komunikacji zdalnej V2 nie jest zgodny z V1 (poprzedni stos komunikacji zdalnej). Postępuj zgodnie z instrukcjami w artykule [Uaktualnienie z wersji 1 do V2,](#upgrade-from-remoting-v1-to-remoting-v2) aby uniknąć wpływu na dostępność usługi.

Dostępne są następujące podejścia, aby włączyć stos V2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Użyj atrybutu złożenia, aby użyć stosu V2

Te kroki zmienić kod szablonu, aby użyć stosu V2 przy użyciu atrybutu zestawu.

1. Zmień zasób `"ServiceEndpoint"` punktu `"ServiceEndpointV2"` końcowego z do w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Użyj `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` metody rozszerzenia, aby utworzyć odbiorników komunikacji zdalnej (równe dla V1 i V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Oznacz zestaw, który zawiera interfejsy komunikacji `FabricTransportServiceRemotingProvider` zdalnej z atrybutem.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Żadne zmiany kodu nie są wymagane w projekcie klienta.
Skompiluj zestaw klienta za pomocą zestawu interfejsu, aby upewnić się, że używany jest wcześniej wyświetlany atrybut zestawu.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Użyj jawnych klas V2, aby użyć stosu V2

Jako alternatywę dla przy użyciu atrybutu zestawu, stos V2 również można włączyć przy użyciu jawnych klas V2.

Te kroki zmienić kod szablonu, aby użyć stosu V2 przy użyciu jawnych klas V2.

1. Zmień zasób `"ServiceEndpoint"` punktu `"ServiceEndpointV2"` końcowego z do w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Użyj [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` z obszaru nazw.

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

3. Użyj [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` z obszaru nazw do tworzenia klientów.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Uaktualnienie z komunikacji zdalnej V1 do komunikacji zdalnej V2

Aby uaktualnić z V1 do V2, wymagane są dwuetapowe uaktualnienia. Wykonaj kroki opisane w tej kolejności.

1. Uaktualnij usługę V1 do usługi V2 przy użyciu tego atrybutu.
Ta zmiana zapewnia, że usługa nasłuchuje na odbiorniku V1 i V2.

    a. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Użyj następującej metody rozszerzenia, aby utworzyć odbiornik komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    d. Dodaj atrybut zestawu na interfejsach komunikacji zdalnej, aby użyć odbiornika V1 i V2 oraz klienta V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Uaktualnij klienta V1 do klienta V2 przy użyciu atrybutu klienta V2.
Ten krok zapewnia, że klient używa stosu V2.
Nie jest wymagana żadna zmiana projektu/usługi klienta. Tworzenie projektów klientów ze zaktualizowanym zestawem interfejsu jest wystarczające.

3. Ten krok jest opcjonalny. Użyj atrybutu odbiornika V2, a następnie uaktualnij usługę V2.
Ten krok zapewnia, że usługa nasłuchuje tylko na odbiorniku V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Korzystanie ze stosu komunikacji zdalnej V2 (zgodnej z interfejsem)

 Stos komunikacji zdalnej V2 (kompatybilny z interfejsem, znany jako V2_1) ma wszystkie funkcje stosu komunikacji zdalnej V2. Jego stos interfejsu jest kompatybilny z układem komunikacji zdalnej V1, ale nie jest wstecznie kompatybilny z V2 i V1. Aby uaktualnić z wersji 1 do V2_1 bez wpływu na dostępność usługi, wykonaj kroki opisane w artykule Uaktualnienie z wersji 1 do V2 (zgodne z interfejsem).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Użyj atrybutu złożenia, aby użyć stosu komunikacji zdalnej V2 (zgodnej z interfejsem)

Wykonaj następujące kroki, aby zmienić stos V2_1.

1. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Użyj metody rozszerzenia komunikacji zdalnej, aby utworzyć odbiornik komunikacji zdalnej.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Dodaj [atrybut zestawu](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) w interfejsach komunikacji zdalnej.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Żadne zmiany nie są wymagane w projekcie klienta.
Skompiluj zestaw klienta z zestawem interfejsu, aby upewnić się, że używany jest poprzedni atrybut zestawu.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Użyj jawnych klas komunikacji zdalnej, aby utworzyć fabrykę odbiornika/klienta dla wersji V2 (zgodna z interfejsem)

Wykonaj następujące kroki:

1. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Użyj [odbiornika komunikacji zdalnej V2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Domyślna nazwa zasobu punktu końcowego usługi używana jest "ServiceEndpointV2_1". Musi być zdefiniowany w manifeście usługi.

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

3. Użyj fabryki [klienta](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)V2 .
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Uaktualnienie z komunikacji zdalnej V1 do komunikacji zdalnej V2 (kompatybilne z interfejsem)

Aby uaktualnić z wersji 1 do V2 (zgodne z interfejsem, znany jako V2_1), wymagane są dwuetapowe uaktualnienia. Wykonaj kroki opisane w tej kolejności.

> [!NOTE]
> Podczas uaktualniania z V1 do `Remoting` V2 upewnij się, że obszar nazw jest aktualizowany do używania V2. Przykład: "Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client"
>
>

1. Uaktualnij usługę V1 do V2_1 usługi przy użyciu następującego atrybutu.
Ta zmiana zapewnia, że usługa nasłuchuje w wersji 1 i V2_1 odbiornika.

    a. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Użyj następującej metody rozszerzenia, aby utworzyć odbiornik komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    d. Dodaj atrybut zestawu na interfejsach komunikacji zdalnej, aby użyć V1, V2_1 odbiornika i V2_1 klienta.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Uaktualnij klienta V1 do klienta V2_1 przy użyciu atrybutu V2_1 klienta.
Ten krok upewnia się, że klient używa stosu V2_1.
Nie jest wymagana żadna zmiana projektu/usługi klienta. Tworzenie projektów klientów ze zaktualizowanym zestawem interfejsu jest wystarczające.

3. Ten krok jest opcjonalny. Usuń wersję odbiornika V1 z atrybutu, a następnie uaktualnij usługę V2.
Ten krok zapewnia, że usługa nasłuchuje tylko na odbiorniku V2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Używanie niestandardowej serializacji z zawiniętym komunikatem komunikacji zdalnej

Dla komunikacji zdalnej zawinięte wiadomości, tworzymy pojedynczy zawinięty obiekt ze wszystkimi parametrami jako pole w nim.
Wykonaj następujące kroki:

1. Zaimplementuj interfejs, `IServiceRemotingMessageSerializationProvider` aby zapewnić implementację do niestandardowych serializacji.
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

2. Zastądź domyślnego dostawcy `JsonSerializationProvider` serializacji dla odbiornika komunikacji zdalnej.

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

3. Zastąd w przypadku `JsonSerializationProvider` fabryki klienta zdalnego zastąrzeć domyślny dostawca serializacji.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Następne kroki

* [Internetowy interfejs API z owin w niezawodnych usługach](service-fabric-reliable-services-communication-webapi.md)
* [Komunikacja z Programem Windows Communication Foundation z niezawodnymi usługami](service-fabric-reliable-services-communication-wcf.md)
* [Bezpieczna komunikacja dla niezawodnych usług](service-fabric-reliable-services-secure-communication.md)
