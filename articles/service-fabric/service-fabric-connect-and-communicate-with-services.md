---
title: Łączenie się z usługami w sieci szkieletowej usług Azure i komunikowanie się z nią
description: Dowiedz się, jak rozwiązywać problemy, łączyć się i komunikować się z usługami w sieci szkieletowej usług.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: e57d169decf482f8b8be1e3b31a07690bc222c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458229"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Łączenie się z usługami w sieci szkieletowej usług i komunikowanie się z nią
W sieci szkieletowej usług usługa działa gdzieś w klastrze sieci szkieletowej usług, zazwyczaj rozproszone między wieloma maszynami wirtualnymi. Może być przenoszony z jednego miejsca do drugiego, przez właściciela usługi lub automatycznie przez sieci szkieletowej usług. Usługi nie są statycznie powiązane z określoną maszyną lub adresem.

Aplikacja sieci szkieletowej usług zazwyczaj składa się z wielu różnych usług, gdzie każda usługa wykonuje specjalistyczne zadanie. Te usługi mogą komunikować się ze sobą w celu utworzenia pełnej funkcji, takiej jak renderowanie różnych części aplikacji sieci web. Istnieją również aplikacje klienckie, które łączą się z usługami i komunikują się z nią. W tym dokumencie omówiono sposób konfigurowania komunikacji z usługami i między nimi w sieci szkieletowej usług.

## <a name="bring-your-own-protocol"></a>Przynieś własny protokół
Sieci szkieletowej usług pomaga zarządzać cyklem życia usług, ale nie podejmuje decyzji o tym, co usługi zrobić. Obejmuje to komunikację. Gdy usługa jest otwierana przez sieć szkieletową usług, jest to możliwość skonfigurowania punktu końcowego dla żądań przychodzących przy użyciu dowolnego protokołu lub stosu komunikacji, który chcesz. Usługa będzie nasłuchiwanie na normalny adres **IP:port** przy użyciu dowolnego schematu adresowania, takich jak identyfikator URI. Wiele wystąpień lub replik usługi może współużytkować proces hosta, w którym to przypadku będą one musiały użyć różnych portów lub użyć mechanizmu udostępniania portów, takich jak sterownik jądra http.sys w systemie Windows. W obu przypadkach każde wystąpienie usługi lub replika w procesie hosta musi być jednoznacznie adresowalna.

![punkty końcowe usługi][1]

## <a name="service-discovery-and-resolution"></a>Odnajdowanie i rozwiązywanie usług
W systemie rozproszonym usługi mogą z czasem przenosić się z jednego komputera na drugą. Może się to zdarzyć z różnych powodów, w tym równoważenia zasobów, uaktualnień, pracy awaryjnej lub skalowania w poziomie. Oznacza to, że adresy punktów końcowych usługi zmieniają się w miarę przenoszenia usługi do węzłów o różnych adresach IP i mogą być otwierane na różnych portach, jeśli usługa korzysta z dynamicznie wybranego portu.

![Dystrybucja usług][7]

Usługa Sieci szkieletowej zapewnia usługę odnajdywania i rozpoznawania o nazwie Usługa nazewnictwa. Usługa nazewnictwa przechowuje tabelę, która mapuje nazwane wystąpienia usługi na adresy punktów końcowych, na których nasłuchują. Wszystkie nazwane wystąpienia usług w sieci szkieletowej usług mają unikatowe nazwy reprezentowane jako identyfikatory URI, `"fabric:/MyApplication/MyService"`na przykład . Nazwa usługi nie zmienia się w okresie istnienia usługi, to tylko adresy punktów końcowych, które można zmienić podczas przenoszenia usług. Jest to analogiczne do witryn sieci Web, które mają stałe adresy URL, ale gdzie adres IP może ulec zmianie. Podobnie jak w sieci DNS, która rozpoznaje adresy URL witryn sieci Web na adresy IP, sieć szkieletowa usług ma rejestratora, który mapuje nazwy usług na adres punktu końcowego.

![punkty końcowe usługi][2]

Rozwiązywanie i łączenie się z usługami obejmuje następujące kroki uruchamiane w pętli:

* **Resolve:** Pobierz punkt końcowy, który usługa opublikowała z usługi nazewnictwa.
* **Połącz:** Połącz się z usługą za pomocą dowolnego protokołu używanego w tym punkcie końcowym.
* **Ponów próbę:** Próba połączenia może zakończyć się niepowodzeniem z dowolnej liczby powodów, na przykład jeśli usługa została przeniesiona od czasu ostatniego rozwiązania adresu punktu końcowego. W takim przypadku poprzednie kroki rozwiązywania i łączenia muszą zostać ponowione, a ten cykl jest powtarzany, dopóki połączenie nie powiedzie się.

## <a name="connecting-to-other-services"></a>Łączenie się z innymi usługami
Usługi łączące się ze sobą wewnątrz klastra zazwyczaj mogą bezpośrednio uzyskiwać dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze znajdują się w tej samej sieci lokalnej. Aby ułatwić łączenie się między usługami, sieci szkieletowej usług zapewnia dodatkowe usługi, które korzystają z usługi nazewnictwa. Usługa DNS i usługa odwrotnego serwera proxy.


### <a name="dns-service"></a>Usługa DNS
Ponieważ wiele usług, zwłaszcza konteneryzowanych usług, może mieć istniejącą nazwę adresu URL, możliwość ich rozwiązania przy użyciu standardowego protokołu DNS (a nie protokołu usługi nazewnictwa) jest bardzo wygodna, szczególnie w scenariuszach "lift and shift" aplikacji. To jest dokładnie to, co robi usługa DNS. Umożliwia mapowanie nazw DNS na nazwę usługi, a tym samym rozpoznawanie adresów IP punktu końcowego. 

Jak pokazano na poniższym diagramie, usługa DNS działająca w klastrze sieci szkieletowej usług mapuje nazwy DNS na nazwy usług, które są następnie rozpoznawane przez usługę nazewnictwa w celu zwrócenia adresów punktów końcowych, z którymi mają się łączyć. Nazwa DNS usługi jest podana w momencie tworzenia. 

![punkty końcowe usługi][9]

Aby uzyskać więcej informacji na temat korzystania z usługi DNS, zobacz [usługę DNS w usłudze Azure Service Fabric.](service-fabric-dnsservice.md)

### <a name="reverse-proxy-service"></a>Usługa odwrotnego serwera proxy
Odwrócony serwer proxy adresuje usługi w klastrze, który udostępnia punkty końcowe HTTP, w tym HTTPS. Odwrotny serwer proxy znacznie upraszcza wywoływanie innych usług i ich metod, mając określony format identyfikatora URI i obsługuje resolve, connect, ponawiać kroki wymagane dla jednej usługi do komunikowania się z inną za pomocą usługi nazewnictwa. Innymi słowy ukrywa usługę nazewnictwa przed tobą podczas wywoływania innych usług, czyniąc to tak proste, jak wywołanie adresu URL.

![punkty końcowe usługi][10]

Aby uzyskać więcej informacji na temat korzystania z usługi odwrotnego serwera proxy zobacz [Reverse proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy.md) artykułu.

## <a name="connections-from-external-clients"></a>Połączenia z klientami zewnętrznymi
Usługi łączące się ze sobą wewnątrz klastra zazwyczaj mogą bezpośrednio uzyskiwać dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze znajdują się w tej samej sieci lokalnej. W niektórych środowiskach jednak klaster może znajdować się za modułem równoważenia obciążenia, który kieruje ruch przychodzący na ruch przychodzący za pośrednictwem ograniczonego zestawu portów. W takich przypadkach usługi nadal mogą komunikować się ze sobą i rozwiązywać adresy za pomocą usługi nazewnictwa, ale należy podjąć dodatkowe kroki, aby umożliwić klientom zewnętrznym łączenie się z usługami.

## <a name="service-fabric-in-azure"></a>Sieci szkieletowej usług na platformie Azure
Klaster sieci szkieletowej usług na platformie Azure jest umieszczany za modułem równoważenia obciążenia platformy Azure. Cały ruch zewnętrzny do klastra musi przechodzić przez moduł równoważenia obciążenia. Moduł równoważenia obciążenia automatycznie prześli ruch przychodzący na danym porcie do losowego *węzła,* który ma ten sam port otwarty. Moduł równoważenia obciążenia platformy Azure wie tylko o portach otwartych w *węzłach,* nie wie o portach otwartych przez poszczególne *usługi.*

![Topologia równoważenia obciążenia i sieci szkieletowej usług azure][3]

Na przykład, aby zaakceptować ruch zewnętrzny na porcie **80,** należy skonfigurować następujące elementy:

1. Napisz usługę, która nasłuchuje na porcie 80. Skonfiguruj port 80 w service's ServiceManifest.xml i otwórz odbiornik w usłudze, na przykład własny serwer sieci web.

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
2. Utwórz klaster sieci szkieletowej usług na platformie Azure i określ port **80** jako niestandardowy port punktu końcowego dla typu węzła, który będzie obsługiwał usługę. Jeśli masz więcej niż jeden typ węzła, można skonfigurować *ograniczenie umieszczania* w usłudze, aby upewnić się, że działa tylko na typ węzła, który ma otwarty port niestandardowego punktu końcowego.

    ![Otwieranie portu w typie węzła][4]
3. Po utworzeniu klastra skonfiguruj moduł równoważenia obciążenia platformy Azure w grupie zasobów klastra, aby przesyłać dalej ruch na porcie 80. Podczas tworzenia klastra za pośrednictwem witryny Azure portal jest to konfigurowane automatycznie dla każdego niestandardowego portu punktu końcowego, który został skonfigurowany.

    ![Ruch do przodu w modułze równoważenia obciążenia platformy Azure][5]
4. Moduł równoważenia obciążenia platformy Azure używa sondy do określenia, czy ma być wysyłany ruch do określonego węzła. Sonda okresowo sprawdza punkt końcowy w każdym węźle, aby ustalić, czy węzeł odpowiada. Jeśli sonda nie może odebrać odpowiedzi po skonfigurowanej liczbie razy, moduł równoważenia obciążenia przestaje wysyłać ruch do tego węzła. Podczas tworzenia klastra za pośrednictwem witryny Azure portal, sonda jest automatycznie konfigurowany dla każdego niestandardowego portu punktu końcowego, który został skonfigurowany.

    ![Ruch do przodu w modułze równoważenia obciążenia platformy Azure][8]

Należy pamiętać, że moduł równoważenia obciążenia platformy Azure i sonda wiedzą tylko o *węzłach,* a nie *o usługach* uruchomionych w węzłach. Moduł równoważenia obciążenia platformy Azure zawsze będzie wysyłać ruch do węzłów, które odpowiadają na sondę, dlatego należy zachować ostrożność, aby upewnić się, że usługi są dostępne w węzłach, które są w stanie odpowiedzieć na sondę.

## <a name="reliable-services-built-in-communication-api-options"></a>Niezawodne usługi: wbudowane opcje interfejsu API komunikacji
Struktura niezawodnych usług jest dostarczana z kilkoma wstępnie utworzonymi opcjami komunikacji. Decyzja o tym, który z nich będzie działać najlepiej dla Ciebie zależy od wyboru modelu programowania, struktury komunikacji i języka programowania, w którym są napisane usługi.

* **Brak konkretnego protokołu:**  Jeśli nie masz określonego wyboru struktury komunikacji, ale chcesz, aby coś szybko uruchomić, idealnym rozwiązaniem dla Ciebie jest [zdalne sterowanie usługami,](service-fabric-reliable-services-communication-remoting.md)co pozwala na silnie wpisane zdalne procedury wywołania niezawodnych usług i wiarygodnych aktorów. Jest to najprostszy i najszybszy sposób na rozpoczęcie komunikacji serwisowej. Obsługa komunikacji zdalnej usługi obsługuje rozpoznawanie adresów usług, połączenia, ponawiania prób i obsługi błędów. Jest to dostępne dla aplikacji języka C# i Java.
* **HTTP**: W przypadku komunikacji niezależnej od języka protokół HTTP zapewnia standardowy wybór w branży z narzędziami i serwerami HTTP dostępnymi w wielu różnych językach, wszystkie obsługiwane przez sieć szkieletową usług. Usługi mogą używać dowolnego dostępnego stosu HTTP, w tym [ASP.NET interfejsu API sieci Web](service-fabric-reliable-services-communication-webapi.md) dla aplikacji języka C#. Klienci zapisani w języku C# mogą `ICommunicationClient` korzystać z klas i, `ServicePartitionClient` podczas gdy w przypadku języka Java używaj `CommunicationClient` klas i `FabricServicePartitionClient` klas, do rozpoznawania [usług, połączeń HTTP i pętli ponawiania prób.](service-fabric-reliable-services-communication.md)
* **WCF:** Jeśli masz istniejący kod, który używa WCF jako `WcfCommunicationListener` struktury komunikacji, `WcfCommunicationClient` a `ServicePartitionClient` następnie można użyć dla strony serwera i klas dla klienta. Jest to jednak dostępne tylko dla aplikacji języka C# w klastrach opartych na systemie Windows. Aby uzyskać więcej informacji, zobacz ten artykuł na temat [implementacji stosu komunikacji opartej na WCF.](service-fabric-reliable-services-communication-wcf.md)

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Korzystanie z niestandardowych protokołów i innych struktur komunikacji
Usługi mogą używać dowolnego protokołu lub struktury do komunikacji, niezależnie od tego, czy jest to niestandardowy protokół binarny za pośrednictwem gniazd TCP, czy przesyłania strumieniowego zdarzeń za pośrednictwem [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) lub [Usługi Azure IoT Hub.](https://azure.microsoft.com/services/iot-hub/) Sieć szkieletowa usług udostępnia interfejsy API komunikacji, do których można podłączyć stos komunikacji, podczas gdy cała praca do odnajdowania i łączenia jest pobierana od Ciebie. Zobacz ten artykuł na temat [modelu komunikacji niezawodnej usługi,](service-fabric-reliable-services-communication.md) aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o pojęciach i interfejsach API dostępnych w [modelu komunikacji Reliable Services,](service-fabric-reliable-services-communication.md)a następnie szybko rozpocznij pracę z [komunikacją zdalną usługi](service-fabric-reliable-services-communication-remoting.md) lub przejdź dogłębnie, aby dowiedzieć się, jak napisać odbiornik komunikacji za pomocą interfejsu API sieci Web za pomocą [hosta własnego OWIN.](service-fabric-reliable-services-communication-webapi.md)

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
