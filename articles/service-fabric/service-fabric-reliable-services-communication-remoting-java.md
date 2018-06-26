---
title: Remoting Service przy użyciu języka Java w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Sieć szkieletowa usług zdalnych umożliwia klientów i usług do komunikowania się z usługami Java za pomocą zdalnego wywołania procedury.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 3215ee4adf907524626b4919b637ce23b9e0e782
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750184"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Remoting Service w języku Java z usługami Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Dla usług, które nie są powiązane protokołu komunikacyjnego konkretnego lub stosu, takie jak WebAPI, Windows Communication Foundation (WCF) lub innych osób, w ramach niezawodnej usługi udostępnia mechanizm komunikacji zdalnej szybkie i łatwe Konfigurowanie zdalnego wywołania procedury dla usługi.  W tym artykule omówiono sposób konfigurowania zdalnego wywołania procedury dla usług napisany za pomocą języka Java.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej usługi
Konfigurowanie komunikacji zdalnej usługi odbywa się w dwóch prostych krokach:

1. Utwórz interfejs do implementacji usługi. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w usłudze. Metody muszą być zwracanie zadań metod asynchronicznych. Musi implementować interfejs `microsoft.serviceFabric.services.remoting.Service` sygnalizują, że usługa ma interfejs usług zdalnych.
2. Użyj odbiornika usługi zdalne w usłudze. Jest to `CommunicationListener` implementację, która zapewnia możliwości komunikacji zdalnej. `FabricTransportServiceRemotingListener` można utworzyć odbiornik komunikacji zdalnej przy użyciu protokołu transportu domyślnego komunikacji zdalnej.

Na przykład następującej usługi bezstanowej przedstawia jedną metodę można uzyskać za pośrednictwem zdalnego wywołania procedury "Hello World".

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
> Argumenty i typy zwracane w interfejsie usługi może być żadnych typów prosty, złożonych lub niestandardowy, ale musi być możliwy do serializacji.
>
>

## <a name="call-remote-service-methods"></a>Wywołanie metody zdalnej usługi
Wywołanie metod w usłudze przy użyciu stosu komunikacji zdalnej odbywa się za pomocą lokalnego serwera proxy do usługi za pośrednictwem `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klasy. `ServiceProxyBase` Metoda tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługę. Z tego serwera proxy możesz po prostu wywołać metod w interfejsie zdalnie.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

W ramach usług zdalnych propaguje wyjątków zgłaszanych na usługę do klienta. Logika sposób obsługi wyjątków po stronie klienta przy użyciu `ServiceProxyBase` bezpośrednio może obsłużyć wyjątki, które usługa zgłasza wyjątek.

## <a name="service-proxy-lifetime"></a>Okres istnienia usługi serwera Proxy
Tworzenie ServiceProxy jest operacją lekkie, można utworzyć dowolną liczbę potrzebnych. Tak długo, jak jest to konieczne, mogą być ponownie używane wystąpień usługi Serwer Proxy. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można ponownie użyć tego samego wystąpienia serwera proxy. Każdy ServiceProxy zawiera komunikacji klienta używany do wysyłania wiadomości przez sieć. Podczas wywoływania wywołań zdalnych, wewnętrzny są sprawdzane ustalenie, czy klient komunikacji jest prawidłowy. Komunikacja klienta na podstawie wyników tych kontroli, zostaje odtworzone w razie potrzeby. W związku z tym, jeśli wystąpi wyjątek, nie trzeba ponownie utworzyć `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) fabryki, która tworzy proxy dla różnych usług zdalnych interfejsów. Jeśli używasz interfejsu API `ServiceProxyBase.create` tworzenia serwera proxy, framework utworzy `FabricServiceProxyFactory`.
Warto utworzyć jedną ręcznie, gdy trzeba zastąpić [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) właściwości.
Fabryka jest kosztowna operacja. `FabricServiceProxyFactory` obsługuje pamięć podręczną komunikacji klientów.
Najlepszym rozwiązaniem jest pamięć podręczna `FabricServiceProxyFactory` tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Obsługa wyjątków komunikacji zdalnej
Zdalne wyjątek zgłoszony przez interfejs API usługi, są wysyłane do klienta jako runtimeexception — lub FabricException.

ServiceProxy obsłużyć wszystkie wyjątki trybu Failover jest tworzony dla partycji usługi. Umożliwia rozwiązanie ponownie punkty końcowe w przypadku Exceptions(Non-Transient Exceptions) trybu Failover i ponowi próbę połączenia z właściwego punktu końcowego. Liczba ponownych prób dla trybu failover wyjątku jest nieokreślony.
W przypadku TransientExceptions tylko ponowną wywołania.

Domyślne parametry ponawiania są zachowywane przez [OperationRetrySettings]. (https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) Te wartości można skonfigurować przez przekazanie obiektu OperationRetrySettings ServiceProxyFactory konstruktora.

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczenia komunikacji niezawodnej usług](service-fabric-reliable-services-secure-communication-java.md)
