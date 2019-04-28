---
title: Łączenie i komunikować się z usługami Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać, łączenie i komunikować się z usługami w usłudze Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 55a0a1a8097ea46c7a3407b5f42824973edcf1a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882329"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Łączenie i komunikować się z usługami w usłudze Service Fabric
W usłudze Service Fabric usługa jest uruchamiana gdzieś w klastrze usługi Service Fabric, zazwyczaj dystrybuowane w wielu maszyn wirtualnych. Można ją przenosić z jednego miejsca do innego, przez właściciela usługi lub automatycznie przez usługę Service Fabric. Usługi nie są statycznie związany z określonego komputera lub adres.

Aplikacja usługi Service Fabric zazwyczaj składa się z wielu różnych usług, w którym każda usługa wykonuje specjalne zadanie. Te usługi mogą komunikować się ze sobą w celu utworzenia pełne funkcji, np. renderowania różne części aplikacji sieci web. Dostępne są także aplikacji klienckich, które nawiązać połączenie i komunikować się z usługami. W tym dokumencie omówiono sposób konfigurowania komunikacji przy użyciu oraz między usługami w usłudze Service Fabric.

## <a name="bring-your-own-protocol"></a>Przenieś swoje własne protokołu
Usługa Service Fabric pomaga zarządzać cyklem życia Twoich usług, ale go nie należy podejmować decyzji dotyczących działania usługi. Dotyczy to również komunikacji. Po otwarciu usługi przez usługę Service Fabric, który jest możliwość usługi konfigurowania punktu końcowego dla żądań przychodzących za pomocą dowolnych stosu protokołu lub komunikacji chcesz. Usługa będzie nasłuchiwać normalnego **IP:port** adresu, przy użyciu dowolnego schematu adresowania, takie jak identyfikator URI. Wiele wystąpień usługi lub replik może udostępniać procesu hosta, w którym to przypadku albo muszą używało innych portów, lub użyj mechanizm współużytkowania portów, takie jak sterownik http.sys jądra w Windows. W obu przypadkach każdego wystąpienia usługi lub replik w procesie hosta musi być unikatowo adresowane.

![punkty końcowe usługi][1]

## <a name="service-discovery-and-resolution"></a>Odnajdowanie usługi i rozwiązania
W rozproszonym systemie usług mogą przenieść z jednej maszyny na inną wraz z upływem czasu. Może to nastąpić z różnych powodów, takich jak równoważenia uaktualnień, pracy w trybie Failover lub skalowania w poziomie zasobów. Oznacza to, że adresy punktów końcowych usługi zmianę w usłudze przenosi do węzłów z różnymi adresami IP oraz mogą otwierać na różnych portów, jeśli usługa używa dynamicznie wybrany port.

![Dystrybucja usług][7]

Usługa Service Fabric udostępnia usługę odnajdywania i rozwiązywania o nazwie Usługa nazewnictwa. Usługa nazewnictwa przechowuje tabelę, która mapuje nazwanych wystąpień usług adresy punktów końcowych, które mogą nasłuchiwać. Wszystkie wystąpienia nazwaną usługę w usłudze Service Fabric mieć unikatowe nazwy reprezentowane jako identyfikatory URI, na przykład `"fabric:/MyApplication/MyService"`. Nazwa usługi nie zmienia się w okresie istnienia usługi, jest tylko adresy punktów końcowych, które mogą być zmieniane podczas przenoszenia usług. Jest to analogiczne do witryn sieci Web, które mają stałe adresy URL, ale których adres IP mogą ulec zmianie. I podobne do serwera DNS w sieci web, który jest rozpoznawany jako adresy URL witryn internetowych adresów IP, Usługa Service Fabric ma rejestratora, która mapuje nazwy usługi do ich adres punktu końcowego.

![punkty końcowe usługi][2]

Rozpoznawanie i łączenie się z usługami obejmuje następujące kroki, które są uruchamiane w pętli:

* **Rozwiąż**: Pobierz punkt końcowy, który został opublikowany usługi z usługi nazw.
* **Połącz**: Połączenia z usługą, niezależnie od protokołu używa w tym punkcie końcowym.
* **Ponów próbę wykonania**: Próba połączenia może zakończyć się niepowodzeniem dla dowolnej liczby powodów, na przykład jeśli usługi zostały przeniesione od czasu ostatniego adresu punktu końcowego został rozwiązany. W takim przypadku poprzedniego rozwiązania i połączyć kroki potrzebę ponowienie próby, a ten cykl jest powtarzany, dopóki połączenie powiedzie się.

## <a name="connecting-to-other-services"></a>Łączenie się z innymi usługami
Łączenie ze sobą ogólnie wewnątrz klastra usługi można uzyskać dostęp do punktów końcowych w innych usługach ponieważ węzłów w klastrze znajdują się w tej samej sieci lokalnej. Aby łatwiej łączyć między usługami, Usługa Service Fabric udostępnia dodatkowe usługi, które korzystają z usługi nazw. Usługi DNS i usługa zwrotnego serwera proxy.


### <a name="dns-service"></a>Usługa DNS
Od wielu usług zwłaszcza konteneryzowanych usług może mieć nazwę istniejącego adresu URL, możliwość je rozwiązać przy użyciu standardowego systemu DNS protokołu (zamiast protokołu nazewnictwa Service) jest bardzo wygodne, szczególnie w przypadku scenariuszy aplikacji "metodą lift and shift". Jest to dokładnie, jak działa usługa DNS. Umożliwia mapowanie nazw DNS na nazwę usługi i dlatego rozpoznawać adresy IP punktów końcowych. 

Jak pokazano na poniższym diagramie, usługi DNS działające w klastrze usługi Service Fabric mapuje nazwy DNS nazwy usługi, które następnie są rozwiązywane przez usługę nazewnictwa do zwrócenia adresy punktów końcowych, aby nawiązać połączenie. Nazwa DNS usługi znajduje się w czasie tworzenia. 

![punkty końcowe usługi][9]

Aby uzyskać więcej informacji na temat sposobu korzystania z serwera DNS usługi zobacz [usługa DNS w usłudze Azure Service Fabric](service-fabric-dnsservice.md) artykułu.

### <a name="reverse-proxy-service"></a>Zwrotny serwer proxy usługi
Zwrotny serwer proxy dotyczy usług w klastrze, który uwidacznia punkty końcowe HTTP, łącznie z protokołu HTTPS. Zwrotny serwer proxy znacznie upraszcza wywołującego innych usług i ich metod, o określonym formacie identyfikatora URI i obsługuje rozpoznania, połączenia, ponów próbę wykonania kroków wymaganych do jednej usługi do komunikowania się z innego za pomocą usługi nazewnictwa. Innymi słowy ukrywa usługi nazewnictwa od użytkownika podczas wywoływania innych usług, wprowadzając to tak proste, jak wywołanie adresu URL.

![punkty końcowe usługi][10]

Aby uzyskać więcej informacji na temat sposobu korzystania z usługi zwrotny serwer proxy, zobacz [zwrotny serwer proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy.md) artykułu.

## <a name="connections-from-external-clients"></a>Połączenia z klientami zewnętrznymi
Łączenie ze sobą ogólnie wewnątrz klastra usługi można uzyskać dostęp do punktów końcowych w innych usługach ponieważ węzłów w klastrze znajdują się w tej samej sieci lokalnej. W niektórych środowiskach, klaster może być za modułem równoważenia obciążenia, która kieruje ruch zewnętrzny ruch przychodzący przez ograniczony zestaw portów. W takich przypadkach usługi nadal mogą komunikować się ze sobą i rozpoznać adresów przy użyciu usługi nazewnictwa, ale dodatkowe kroki należy podjąć, aby umożliwić zewnętrznych klientom łączenie się z usługami.

## <a name="service-fabric-in-azure"></a>Usługa Service Fabric na platformie Azure
Klaster usługi Service Fabric na platformie Azure jest umieszczany za modułem równoważenia obciążenia platformy Azure. Cały ruch zewnętrzny do klastra musi przechodzić przez moduł równoważenia obciążenia. Modułu równoważenia obciążenia automatycznie przekazują ruch przychodzący na danym porcie do losową *węzła* ma tego samego portu, Otwórz. Usługi Azure Load Balancer tylko zna porty otwarte na *węzłów*, nie wie o otwarte porty przez osobę *usług*.

![Usługa Azure topologii równoważenia obciążenia i usługi Service Fabric][3]

Na przykład w celu akceptowania ruchu zewnętrznego na porcie **80**, muszą być skonfigurowane następujące elementy:

1. Napisz to usługa, która nasłuchuje na porcie 80. Skonfiguruj port 80 w ServiceManifest.xml usługi, a następnie otwórz odbiornik usługi, na przykład serwer samodzielnie hostowanej sieci web.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Utwórz klaster usługi Service Fabric na platformie Azure i określ port **80** jako port niestandardowy punkt końcowy dla typu węzła, który będzie hostować usługę. Jeśli masz więcej niż jeden typ węzła, możesz skonfigurować *ograniczeń umieszczania* w usłudze, aby upewnić się, że działa tylko dla typu węzła, który ma otworzyć port niestandardowy punkt końcowy.

    ![Otwarcie portu dla typu węzła][4]
3. Po utworzeniu klastra należy skonfigurować Równoważenie obciążenia Azure, w grupie zasobów klastra do przesyłania dalej ruchu na porcie 80. Podczas tworzenia klastra za pośrednictwem witryny Azure portal, to jest automatycznie utworzyć dla każdego portu niestandardowego punktu końcowego, który został skonfigurowany.

    ![Przesyłanie ruchu sieciowego w module równoważenia obciążenia platformy Azure][5]
4. Usługi Azure Load Balancer używa sondy, aby określić, czy chce wysyłać ruch do określonego węzła. Sonda okresowo sprawdza dostępność punktu końcowego w każdym węźle, aby ustalić, czy węzeł odpowiada. Jeśli sonda nie otrzymasz odpowiedzi po określonej liczbie razy, moduł równoważenia obciążenia zaprzestaje wysyłania ruchu do tego węzła. Podczas tworzenia klastra za pośrednictwem witryny Azure portal, sondy automatycznie skonfigurowano dla każdego portu niestandardowego punktu końcowego, który został skonfigurowany.

    ![Przesyłanie ruchu sieciowego w module równoważenia obciążenia platformy Azure][8]

Należy pamiętać, że usługi Azure Load Balancer i badania tylko wiedzieć o *węzłów*, a nie *usług* działających w węzłach. Usługi Azure Load Balancer zawsze będzie wysyłać ruch do węzłów, które odpowiadają sondowania, więc należy uważać, aby upewnić się, że usługi są dostępne w węzły, które są w stanie odpowiadać na sondy.

## <a name="reliable-services-built-in-communication-api-options"></a>Usług Reliable Services: Opcje komunikacji wbudowanej interfejsu API
W ramach usług Reliable Services jest dostarczany z kilku opcji wstępnie skompilowanych komunikacji. Decyzja o tym, które jeden będą najlepiej działać dla Ciebie zależy od wybranego modelu programowania, w ramach komunikacji i języka programowania, które usługi są zapisywane w.

* **Nie określonego protokołu:**  Jeśli nie masz określonego wybór strukturę komunikacji, ale chcesz rozpocząć coś się szybko, a następnie jest doskonałym rozwiązaniem dla Ciebie [zdalna komunikacja usług](service-fabric-reliable-services-communication-remoting.md), co pozwala silnie typizowane procedury zdalnej wymaga Usług Reliable Services i Reliable Actors. Jest to najłatwiejszych i najszybszych obecnie sposobem rozpoczęcia pracy z komunikacji usług. Zdalna komunikacja usług obsługuje rozpoznawanie adresy usługi, połączenia, ponownych prób i obsługi błędów. Ta opcja jest dostępna zarówno dla C# i aplikacji w języku Java.
* **HTTP**: W przypadku komunikacji niezależny od języka HTTP zapewnia wybór będące standardami branżowymi, za pomocą narzędzi i serwerów HTTP jest dostępna w wielu językach obsługiwanych przez usługi Service Fabric. Usługi mogą używać dowolnego stos HTTP niedostępnych, w tym [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) dla C# aplikacji. Klienci napisane w C# mogą korzystać z `ICommunicationClient` i `ServicePartitionClient` klasy, natomiast dla języka Java, należy użyć `CommunicationClient` i `FabricServicePartitionClient` klas, [usługi rozdzielczości, połączeń HTTP i pętli ponawiania](service-fabric-reliable-services-communication.md).
* **WCF**: Jeśli masz istniejący kod, który używa programu WCF jako preferowanej struktury komunikacji, a następnie można użyć `WcfCommunicationListener` po stronie serwera i `WcfCommunicationClient` i `ServicePartitionClient` klasy dla klienta. Jednak jest to możliwe tylko dla C# aplikacji na Windows na podstawie klastrów. Aby uzyskać więcej informacji znajduje się w artykule [implementacji stos komunikacji WCF na podstawie](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Przy użyciu niestandardowych protokołów i innych platform komunikacji
Usługi można użyć dowolnego protokołu lub framework do komunikacji, czy jest to za pośrednictwem gniazda TCP lub przesyłanie strumieniowe zdarzeń za pomocą niestandardowego protokołu binarne [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) lub [usługi Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). Usługa Service Fabric zapewnia komunikację interfejsów API, które można podłączyć stosu komunikacji, podczas gdy cała praca odnajdywanie i połączyć to wyodrębnione ze strony użytkownika. Znajduje się w artykule [model komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat pojęć i interfejsami API dostępnymi w [model komunikacji usług Reliable Services](service-fabric-reliable-services-communication.md), następnie szybko rozpocząć pracę z [zdalna komunikacja usług](service-fabric-reliable-services-communication-remoting.md) lub dokładniejszym, aby dowiedzieć się, jak napisać komunikat odbiornik za pomocą [internetowego interfejsu API z OWIN hosta samodzielnego](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
