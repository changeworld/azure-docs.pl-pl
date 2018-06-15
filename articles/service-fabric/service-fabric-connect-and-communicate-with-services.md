---
title: Połącz i łączyć się z usługami w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać, łączenie i łączyć się z usługami w sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 2b6fd2373a9cd0b376a6c8729d5952c5fc48ddf8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205590"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Połącz i łączyć się z usługami w sieci szkieletowej usług
W sieci szkieletowej usług Usługa gdzieś działa w klastrze usługi sieć szkieletowa, zazwyczaj są rozproszone na wielu maszyn wirtualnych. Można można przenosić z jednego miejsca do innego, przez właściciela usługi lub automatycznie przez sieć szkieletowa usług. Usługi nie są statycznie związane z określonego komputera lub adres.

Aplikacji usługi Service Fabric zazwyczaj składa się z wielu różnych usług, w którym każda usługa wykonuje zadanie specjalne. Te usługi może komunikować się ze sobą do utworzenia pełnej funkcji, takich jak renderowania różne części aplikacji sieci web. Dostępne są także aplikacji klienckich, które nawiązać połączenie i łączyć się z usługami. Tego dokumentu zawiera omówienie sposobu konfigurowania komunikacji z i od usługi w sieci szkieletowej usług.

Ten film Microsoft Virtual Academy omówiono także komunikacji usługi: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=iYFCk76yC_6706218965">  
<img src="./media/service-fabric-connect-and-communicate-with-services/CommunicationVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="bring-your-own-protocol"></a>Przełącz własnego protokołu
Sieć szkieletowa usług ułatwia zarządzanie cyklem życia usługi, ale nie powoduje decyzje na temat działania usługi. W tym komunikacji. Po otwarciu usługi przez sieć szkieletowa usług, który jest możliwość usługi konfigurowania punktu końcowego dla przychodzących żądań przy użyciu dowolnego stosu protokołu lub komunikacji ma. Usługa będzie nasłuchiwać zwykłym **IP:port** adresów przy użyciu dowolnego schematu adresowania, takich jak identyfikator URI. Wiele wystąpień usługi lub replik może udostępniać procesu hosta, w którym to przypadku albo muszą używać różnych portów lub mechanizm współużytkowania portów, takie jak sterownik http.sys jądra systemu Windows. W obu przypadkach musi być unikatowo adresowane każdego wystąpienia usługi lub repliki w ramach procesu hosta.

![Punkty końcowe usługi][1]

## <a name="service-discovery-and-resolution"></a>Odnajdowanie usługi i rozwiązania
W rozproszonym systemie usługi może przenieść z jednego komputera na inny wraz z upływem czasu. Może to nastąpić z różnych powodów, takich jak zasób równoważenia uaktualnień, pracy w trybie Failover lub skalowalnych w poziomie. Oznacza to, że adresy punktów końcowych usługi zmienić, ponieważ usługa przenosi do węzłów z różnych adresów IP i może otwierać na różnych portów, jeśli usługa używa portu dynamicznego wybrane.

![Dystrybucja usług programu][7]

Sieć szkieletowa usług udostępnia usługę odnajdywania i rozwiązania o nazwie Naming Service. Naming Service obsługuje tabelę, która mapuje nazwanych wystąpień usług adresy punktów końcowych, do których one nasłuchiwania. Wszystkie wystąpienia usługi o nazwie w sieci szkieletowej usług mają unikatowe nazwy, reprezentowane jako identyfikatory URI, na przykład `"fabric:/MyApplication/MyService"`. Nazwa usługi nie zmienia się w okresie istnienia usługi, jest tylko adresy punktów końcowych, które można zmienić podczas przenoszenia usług. To jest analogiczne do witryn sieci Web, gdy mają stałe adresy URL, ale gdzie adresu IP mogą ulec zmianie. I podobne w systemie DNS w sieci web, który jest rozpoznawany jako adresy URL witryny sieci Web adresy IP, usługi Service Fabric ma rejestratora, która mapuje nazwy usługi na swój adres punktu końcowego.

![Punkty końcowe usługi][2]

Rozpoznawanie i łączących się z usługami obejmuje następujące kroki uruchamiania w pętli:

* **Rozwiąż**: uzyskanie punktu końcowego, który został opublikowany usługą Naming Service.
* **Połącz**: połączyć się z usługą za pośrednictwem niezależnie od protokołu używa w tym punkcie końcowym.
* **Spróbuj ponownie**: próba połączenia może zakończyć się niepowodzeniem dla dowolnej liczby przyczyn, na przykład jeśli usługi zostały przeniesione od czasu ostatniego adres punktu końcowego został rozwiązany. W takim przypadku poprzedni rozwiązania i połączyć kroki potrzebę ponowione, a ten cykl powtarza się do chwili pomyślnego połączenia.

## <a name="connecting-to-other-services"></a>Nawiązywanie połączenia z innymi usługami
Łączenie ze sobą w klastrze zazwyczaj usług bezpośrednio mają dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze są w tej samej sieci lokalnej. Aby łatwiej łączyć między usługami, Service Fabric zawiera dodatkowe usługi, które korzystają z usługi nazw. Usługa DNS i usługa zwrotnego serwera proxy.


### <a name="dns-service"></a>Usługa DNS
Począwszy od wielu usług zwłaszcza konteneryzowanych usług może mieć nazwę istniejącego adresu URL, możliwość rozwiązać te przy użyciu standardowych DNS protokołu (zamiast protokołu Naming Service) jest bardzo wygodny, szczególnie w scenariuszach "przyrostu i przesunięcia". Jest to dokładnie, jak działa usługa DNS. Umożliwia ona mapowania nazw DNS na nazwę usługi i dlatego rozpoznać adresów IP punktu końcowego. 

Jak pokazano na poniższym diagramie, usługa DNS działa w klastrze usługi sieć szkieletowa mapuje nazwy DNS nazwy usługi, które następnie są rozpoznawane przez usługę nazewnictwa do zwrócenia adresy punktów końcowych, aby nawiązać połączenie. Nazwy DNS dla usługi znajduje się w momencie tworzenia obiektu. 

![Punkty końcowe usługi][9]

Więcej informacji na temat sposobu korzystania z usługi DNS usługi zobacz [usługa DNS w sieci szkieletowej usług Azure](service-fabric-dnsservice.md) artykułu.

### <a name="reverse-proxy-service"></a>Zwrotny serwer proxy usługi
Zwrotnego serwera proxy dotyczy usługi w klastrze, który udostępnia punktów końcowych HTTP, łącznie z protokołu HTTPS. Zwrotny serwer proxy znacząco upraszcza wywoływania innych usług i ich metod przez określony format identyfikatora URI i obsługuje rozpoznania, połączenia, powtórz kroki wymagane do jedną usługę do komunikowania się z inną przy użyciu usługi nazw. Innymi słowy ukrywa on Naming Service od użytkownika podczas wywoływania metody innych usług, dokonując to prosty jak wywołanie adresu URL.

![Punkty końcowe usługi][10]

Więcej informacji na temat sposobu korzystania z usługi zwrotny serwer proxy można znaleźć [odwrotny serwer proxy w sieci szkieletowej usług Azure](service-fabric-reverseproxy.md) artykułu.

## <a name="connections-from-external-clients"></a>Połączenia z klientami zewnętrznymi
Łączenie ze sobą w klastrze zazwyczaj usług bezpośrednio mają dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze są w tej samej sieci lokalnej. W przypadku środowisk jednak klastra może być za modułem równoważenia obciążenia, który przekierowuje ruch przychodzący zewnętrznych za pomocą ograniczonego zestawu portów. W takich przypadkach usługi nadal mogą komunikować się ze sobą i rozpoznawania adresów przy użyciu usługi nazw, ale dodatkowe kroki należy zezwolić zewnętrznych klientom na łączenie się z usługami.

## <a name="service-fabric-in-azure"></a>Sieć szkieletowa usług na platformie Azure
Klastra sieci szkieletowej usług w usłudze Azure znajduje się za usługą równoważenia obciążenia Azure. Wszystkie zewnętrznego ruchu do klastra musi przejść przez moduł równoważenia obciążenia. Moduł równoważenia obciążenia będzie automatycznie przesyłał dalej ruch przychodzący na porcie danego do losowe *węzła* mający ten sam port, Otwórz. Moduł równoważenia obciążenia Azure tylko zna porty otwarty na *węzłów*, nie może określić dotyczących otwarte porty przez osobę *usług*.

![Azure topologii równoważenia obciążenia i sieci szkieletowej usług][3]

Na przykład, aby zaakceptować zewnętrznych ruch na porcie **80**, muszą być skonfigurowane następujące czynności:

1. Zapisu to usługa, która nasłuchuje na porcie 80. Skonfiguruj port 80 w pliku ServiceManifest.xml usługi i otwórz odbiornik usługi, na przykład serwer sieci web siebie.

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
2. Tworzenie klastra sieci szkieletowej usług na platformie Azure i określić port **80** jako port punktu końcowego niestandardowych dla typu węzła, który będzie hostem usługi. Jeśli masz więcej niż jeden typ węzła, można skonfigurować *ograniczenia umieszczania* na usługę, aby upewnić się, że działa tylko na typ węzła mającego otworzyć port punktu końcowego niestandardowych.

    ![Otwarcie portu dla typu węzła][4]
3. Po utworzeniu klastra, należy skonfigurować usługę równoważenia obciążenia Azure w grupie zasobów klastra, aby przesyłał dalej ruch na porcie 80. Podczas tworzenia klastra za pośrednictwem portalu Azure, to jest automatycznie utworzyć dla każdego portu niestandardowego punktu końcowego, który został skonfigurowany.

    ![Ruch do przodu w usłudze równoważenia obciążenia Azure][5]
4. Moduł równoważenia obciążenia Azure używa badanie w celu określenia, czy przesyłają dane do określonego węzła. Sonda okresowo sprawdza, czy punkt końcowy na każdym węźle, aby określić, czy węzeł odpowiada. Jeśli sondy nie można odebrać odpowiedzi od skonfigurowanych wiele razy, usługi równoważenia obciążenia zatrzymuje wysyłania ruchu do tego węzła. Podczas tworzenia klastra za pośrednictwem portalu Azure, badanie automatycznie jest konfigurowane dla poszczególnych portów niestandardowych punktu końcowego, który został skonfigurowany.

    ![Ruch do przodu w usłudze równoważenia obciążenia Azure][8]

Należy pamiętać, że moduł równoważenia obciążenia Azure i badania tylko wiedzieć o *węzłów*, a nie *usług* działających w węzłach. Moduł równoważenia obciążenia Azure będą zawsze wysyłały ruchu do węzłów, które odpowiada sondowania, więc należy uważać, aby upewnić się, że usługi są dostępne w węzłach, które są w stanie odpowiadać na sondę.

## <a name="reliable-services-built-in-communication-api-options"></a>Niezawodnej usługi: Opcje komunikacji wbudowanej interfejsu API
Framework niezawodne usługi jest dostarczany z kilku opcji wbudowanych komunikacji. Decyzja o tym, które jedną będzie najlepsza, zależy od wybór modelu programowania, w ramach komunikacji i język programowania, które usługi są zapisywane w.

* **Nie określonego protokołu:** Jeśli nie ma określonego wybór framework komunikacji, ale chcesz coś się szybkie i uruchomienie, a następnie jest doskonałym rozwiązaniem dla Ciebie [service remoting](service-fabric-reliable-services-communication-remoting.md), dzięki czemu Wywołanie procedury zdalnej jednoznacznie dla Reliable Services i Reliable Actors. Jest to najprostszy i najszybszym sposobem na szybkie wprowadzenie do komunikacji usługi. Service remoting obsługuje rozpoznawanie adresy usługi, połączenia, ponów próbę i obsługa błędów. To jest dostępna dla C# i aplikacji Java.
* **HTTP**: komunikat niezależny od języka HTTP zapewnia wybór standardowych z narzędziami i serwery HTTP dostępne w wielu językach obsługiwanych przez sieć szkieletowa usług. Usługi mogą używać żadnych HTTP stosu, w tym [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) dla aplikacji C#. Napisany w języku C# klienci mogą używać `ICommunicationClient` i `ServicePartitionClient` klas, natomiast dla języka Java, użyj `CommunicationClient` i `FabricServicePartitionClient` klas, [usługi rozdzielczości, połączeń HTTP i ponów próbę wykonania pętli](service-fabric-reliable-services-communication.md).
* **Usługi WCF**: Jeśli masz istniejący kod, który używa WCF jako platforma sieci komunikacji, a następnie można użyć `WcfCommunicationListener` po stronie serwera i `WcfCommunicationClient` i `ServicePartitionClient` klasy dla klienta. To jednak jest dostępna tylko dla aplikacji C# w klastrach z systemem Windows. Aby uzyskać więcej informacji, zobacz ten artykuł [WCF na podstawie wykonania stosu komunikacji](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Przy użyciu protokołów niestandardowych i innych platform komunikacji
Usługi można użyć protokołu lub framework do komunikacji, czy jest protokołem binarne niestandardowych za pośrednictwem gniazda TCP lub za pośrednictwem przesyłania strumieniowego zdarzenia [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) lub [Centrum IoT Azure](https://azure.microsoft.com/services/iot-hub/). Sieć szkieletowa usług zapewnia komunikację interfejsów API stosu komunikacji, można podłączyć podczas całą pracę na potrzeby odnajdywania i połączyć jest pobieranej przez użytkownika. Znajduje się w artykule [model komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md) więcej szczegółów.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat pojęć i interfejsami API dostępnymi w [model komunikacji niezawodnej usługi](service-fabric-reliable-services-communication.md), następnie szybko rozpocząć pracę z [service remoting](service-fabric-reliable-services-communication-remoting.md) lub przejdź szczegółowe informacje na temat zapisu komunikatu przy użyciu odbiornika [interfejsu API sieci Web z hosta samodzielnego OWIN](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
