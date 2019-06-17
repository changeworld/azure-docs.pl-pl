---
title: Zdalna komunikacja usług przy użyciu języka Java w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Komunikacja zdalna usługi Service Fabric umożliwia klientów i usług komunikować się z usługami Java za pomocą zdalnego wywołania procedury.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 51c8c689bd3fe3e8967bab77e776ad02f9cb59f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123639"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Zdalna komunikacja usług w języku Java przy użyciu usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Dla usług, które nie są powiązane protokołu komunikacyjnego określonego lub stos, takie jak WebAPI, Windows Communication Foundation (WCF) lub innych usług Reliable Services w ramach udostępnia mechanizm komunikacji zdalnej szybkie i łatwe Konfigurowanie zdalnego wywołania procedury dla usługi.  W tym artykule omówiono sposób konfigurowania zdalnego wywołania procedury usługi napisany w języku Java.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej usługi
Konfigurowanie komunikacji zdalnej usługi odbywa się w dwa proste kroki:

1. Utwórz interfejs dla usługi w celu wdrożenia. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w Twojej usłudze. Metody muszą być zwracającą zadanie metod asynchronicznych. Interfejs musi implementować `microsoft.serviceFabric.services.remoting.Service` do sygnalizowania, że usługa ma interfejs komunikację zdalną.
2. Użyj odbiornika komunikacji zdalnej w usłudze. Jest to `CommunicationListener` implementację, która oferuje możliwości komunikacji zdalnej. `FabricTransportServiceRemotingListener` można utworzyć odbiornik komunikacji zdalnej przy użyciu domyślnego protokołu transportu komunikacji zdalnej.

Na przykład następujące usługi bezstanowej udostępnia pojedynczą metodę można pobrać "Hello World" za pośrednictwem zdalnego wywołania procedury.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Argumenty i typy zwracane występujące w interfejsie usługi może być żadnych typów prostych, złożonych lub niestandardowego, ale muszą podlegać serializacji.
>
>

## <a name="call-remote-service-methods"></a>Wywoływanie metod usługi zdalnej
Wywoływanie metod w usłudze przy użyciu stosu komunikacji zdalnej jest przeprowadzane za pomocą lokalnego serwera proxy do usługi za pośrednictwem `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klasy. `ServiceProxyBase` Metoda tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługę. Za pomocą tego serwera proxy możesz po prostu wywołać metody w interfejsie zdalnie.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

W ramach komunikacji zdalnej propaguje wyjątki zgłaszane na usługę do klienta. Dlatego obsługa wyjątków logiki po stronie klienta przy użyciu `ServiceProxyBase` bezpośrednio może obsługiwać wyjątki, które zgłasza usługi.

## <a name="service-proxy-lifetime"></a>Okres istnienia usługi serwera Proxy
Tworzenie ServiceProxy jest lekka operacja, aby można było utworzyć dowolną liczbę, według potrzeb. Tak długo, jak są one potrzebne, mogą zostać ponownie użyte wystąpień usługi Serwer Proxy. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można ponownie użyć tego samego wystąpienia serwera proxy. Każdy ServiceProxy zawiera komunikacji klienta używany do wysyłania wiadomości przez sieć. Podczas wywoływania wywołań zdalnych, wewnętrzne są sprawdzane w celu określenia, czy klient komunikacji jest nieprawidłowy. Na podstawie wyników tych kontroli, utworzone ponownie klienta komunikacji, jeśli to konieczne. W związku z tym, jeśli wystąpi wyjątek, nie trzeba ponownie utworzyć `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) fabryki, która tworzy proxy dla różnych usług zdalnych interfejsów. Jeśli używasz interfejsu API `ServiceProxyBase.create` do tworzenia serwera proxy i następnie framework tworzy `FabricServiceProxyFactory`.
Warto utworzyć ręcznie, gdy trzeba zastąpić [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) właściwości.
Fabryka jest kosztowną operacją. `FabricServiceProxyFactory` zachowuje pamięć podręczną komunikacji klientów.
Najlepszym rozwiązaniem jest pamięć podręczna `FabricServiceProxyFactory` tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Komunikacja zdalna obsługa wyjątków
Zdalne wyjątek zgłoszony przez interfejs API usługi, są wysyłane do klienta jako RuntimeException lub FabricException.

ServiceProxy obsługi wszystkich wyjątków trybu Failover dla partycji usługi jest tworzona dla. Ponownie naprawił punktów końcowych w przypadku pracy awaryjnej Exceptions(Non-Transient Exceptions) i ponawia próbę wywołania z właściwego punktu końcowego. Liczba ponownych prób dla trybu failover wyjątek jest nieokreślony.
W przypadku TransientExceptions jego ponawia próbę tylko wywołania.

Domyślne parametry ponawiania prób są zachowywane przez [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Te wartości można skonfigurować przez przekazanie obiektu OperationRetrySettings ServiceProxyFactory konstruktora.

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczenia komunikacji w przypadku usług Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
