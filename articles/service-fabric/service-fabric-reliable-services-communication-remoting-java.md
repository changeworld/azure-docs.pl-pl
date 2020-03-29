---
title: Komunikacji zdalnej usługi przy użyciu języka Java w sieci szkieletowej usług Azure
description: Komunikacji zdalnej sieci szkieletowej usług umożliwia klientom i usługom komunikowanie się z usługami Java przy użyciu zdalnego wywołania procedury.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426636"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Obsługa zdalna usługi w języku Java z niezawodnymi usługami
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

W przypadku usług, które nie są powiązane z określonym protokołem komunikacyjnym lub stosem, takich jak WebAPI, Windows Communication Foundation (WCF) lub inne, struktura niezawodnych usług zapewnia mechanizm komunikacji zdalnej, aby szybko i łatwo skonfigurować zdalne wywołania procedury Usług.  W tym artykule omówiono sposób konfigurowania zdalnych wywołań procedur dla usług napisanych w języku Java.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej w usłudze
Konfigurowanie komunikacji zdalnej dla usługi odbywa się w dwóch prostych krokach:

1. Utwórz interfejs dla usługi do zaimplementowania. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w usłudze. Metody muszą być zwracające zadania metody asynchroniczne. Interfejs musi `microsoft.serviceFabric.services.remoting.Service` zostać zaimplementował, aby zasygnalizować, że usługa ma interfejs komunikacji zdalnej.
2. Użyj odbiornika komunikacji zdalnej w usłudze. Jest to `CommunicationListener` implementacja, która zapewnia możliwości komunikacji zdalnej. `FabricTransportServiceRemotingListener`może służyć do tworzenia odbiornika komunikacji zdalnej przy użyciu domyślnego protokołu transportu komunikacji zdalnej.

Na przykład następująca usługa bezstanowa udostępnia pojedynczą metodę, aby uzyskać "Hello World" za pomocą zdalnego wywołania procedury.

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
> Argumenty i typy zwracane w interfejsie usługi mogą być typumi proste, złożone lub niestandardowe, ale muszą być serializowalne.
>
>

## <a name="call-remote-service-methods"></a>Wywoływanie zdalnych metod serwisu
Wywoływanie metod w usłudze przy użyciu stosu komunikacji zdalnej odbywa `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` się przy użyciu lokalnego serwera proxy do usługi za pośrednictwem klasy. Metoda `ServiceProxyBase` tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługa. Za pomocą tego serwera proxy można po prostu zdalnie wywoływać metody w interfejsie.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Struktura komunikacji zdalnej propaguje wyjątki zgłaszane do usługi do klienta. Tak logiki obsługi wyjątków `ServiceProxyBase` na kliencie przy użyciu można bezpośrednio obsługiwać wyjątki, które zgłasza usługa.

## <a name="service-proxy-lifetime"></a>Okres istnienia serwera proxy usługi
Tworzenie ServiceProxy jest lekką operacją, dzięki czemu można utworzyć dowolną liczbę. Wystąpienia serwera proxy usługi mogą być ponownie używane tak długo, jak są potrzebne. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można ponownie użyć tego samego wystąpienia serwera proxy. Każdy ServiceProxy zawiera klienta komunikacji używane do wysyłania wiadomości przez sieć. Podczas wywoływania wywołań zdalnych wykonywane są kontrole wewnętrzne w celu ustalenia, czy klient komunikacji jest prawidłowy. Na podstawie wyników tych kontroli klient komunikacji jest odtworzony w razie potrzeby. W związku z tym w przypadku wystąpienia wyjątku `ServiceProxy`nie trzeba ponownie utworzyć .

### <a name="serviceproxyfactory-lifetime"></a>Okres istnienia ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) to fabryka, która tworzy serwer proxy dla różnych interfejsów komunikacji zdalnej. Jeśli używasz `ServiceProxyBase.create` interfejsu API do tworzenia `FabricServiceProxyFactory`serwera proxy, a następnie framework tworzy .
Jest to przydatne do utworzenia ręcznie, gdy trzeba zastąpić [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) właściwości.
Fabryka jest kosztowną operacją. `FabricServiceProxyFactory`utrzymuje pamięć podręczną klientów komunikacji.
Najlepszym rozwiązaniem jest `FabricServiceProxyFactory` buforowanie tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Obsługa wyjątków komunikacji zdalnej
Wszystkie zdalne wyjątek zgłoszony przez interfejs API usługi, są wysyłane z powrotem do klienta jako RuntimeException lub FabricException.

ServiceProxy obsługuje wszystkie wyjątek trybu failover dla partycji usługi, dla czego jest tworzony dla. Ponownie rozpoznaje punkty końcowe, jeśli istnieje wyjątki trybu failover(wyjątki nieprzemijające) i ponawia ponawia wywołanie z poprawnym punktem końcowym. Liczba ponownych prób dla trybu failover Wyjątek jest nieokreślony.
W przypadku przejściowych znikomości, tylko ponawia wywołanie.

Domyślne parametry ponawiania próby są provied przez [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Można skonfigurować te wartości, przekazując OperationRetrySettings obiektu do ServiceProxyFactory konstruktora.

## <a name="next-steps"></a>Następne kroki
* [Zabezpieczanie komunikacji dla niezawodnych usług](service-fabric-reliable-services-secure-communication-java.md)
