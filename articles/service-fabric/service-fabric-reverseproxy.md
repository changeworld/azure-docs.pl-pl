---
title: Odwrotny serwer proxy sieci szkieletowej usług Azure
description: Użyj odwrotnego serwera proxy sieci usług do komunikacji z mikrousługami z wewnątrz i na zewnątrz klastra.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 4fa4c6e46dd786b833087f892d995e85b5d2ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282226"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Odwróć serwer proxy w sieci szkieletowej usług Azure
Odwróć serwer proxy wbudowany w usługę Azure Service Fabric ułatwia mikrousługom uruchomionym w klastrze sieci szkieletowej usług odnajdywanie i komunikowanie się z innymi usługami, które mają punkty końcowe http.

## <a name="microservices-communication-model"></a>Model komunikacji mikrousług
Mikrousługi w sieci szkieletowej usług są uruchamiane na podzbiorze węzłów w klastrze i mogą migrować między węzłami z różnych powodów. W rezultacie punkty końcowe dla mikrousług można zmieniać dynamicznie. Aby odnajdować i komunikować się z innymi usługami w klastrze, mikrousługi należy przejść przez następujące kroki:

1. Rozwiąż lokalizację usługi za pośrednictwem usługi nazewnictwa.
2. Połącz się z usługą.
3. Zawijanie poprzednich kroków w pętli, która implementuje rozpoznawanie usług i ponawia próby zasad, aby zastosować błędy połączenia

Aby uzyskać więcej informacji, zobacz [Łączenie się i komunikowanie się z usługami](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikowanie się przy użyciu odwrotnego serwera proxy
Reverse proxy to usługa, która działa w każdym węźle i obsługuje rozpoznawanie punktów końcowych, automatyczne ponawianie próby i inne błędy połączenia w imieniu usług klienta. Odwrotny serwer proxy można skonfigurować do stosowania różnych zasad, ponieważ obsługuje żądania z usług klienta. Za pomocą odwrotnego serwera proxy umożliwia usługi klienta do korzystania z dowolnej bibliotekkomuzysty HTTP po stronie klienta i nie wymaga specjalnego rozpoznawania i ponawiać logikę w usłudze. 

Odwrócony serwer proxy udostępnia jeden lub więcej punktów końcowych w węźle lokalnym dla usług klienta do użycia do wysyłania żądań do innych usług.

![Komunikacja wewnętrzna][1]

> [!NOTE]
> **Obsługiwane platformy**
>
> Odwrotny serwer proxy w sieci szkieletowej usług obsługuje obecnie następujące platformy
> * *Klaster systemu Windows*: Windows 8 i nowsze lub Windows Server 2012 i nowsze
> * *Klaster Linuksa:* Reverse Proxy nie jest obecnie dostępny dla klastrów Linuksa
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Docieranie do mikrousług spoza klastra
Domyślny model komunikacji zewnętrznej dla mikrousług jest modelem opt-in, w którym nie można uzyskać dostępu do każdej usługi bezpośrednio z klientów zewnętrznych. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), który jest granicą sieci między mikrousługami i klientami zewnętrznymi, wykonuje translację adresów sieciowych i przekazuje żądania zewnętrzne do wewnętrznych punktów końcowych IP:port. Aby punkt końcowy mikrousług był bezpośrednio dostępny dla klientów zewnętrznych, należy najpierw skonfigurować moduł równoważenia obciążenia, aby przekazywać dalej ruch do każdego portu używanego przez usługę w klastrze. Ponadto większość mikrousług, zwłaszcza mikrousług stanowych, nie działa na wszystkich węzłach klastra. Mikrousługi mogą przenosić się między węzłami w pracy awaryjnej. W takich przypadkach moduł równoważenia obciążenia nie może skutecznie określić lokalizacji węzła docelowego replik, do których powinien przesyłać dalej ruch.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Docieranie do mikrousług za pośrednictwem odwrotnego serwera proxy spoza klastra
Zamiast konfigurować port pojedynczej usługi w modułze równoważenia obciążenia, można skonfigurować tylko port odwrotnego serwera proxy w modułze równoważenia obciążenia. Ta konfiguracja umożliwia klientom spoza usług dostępu klastra wewnątrz klastra przy użyciu odwrotnego serwera proxy bez dodatkowej konfiguracji.

![Komunikacja zewnętrzna][0]

> [!WARNING]
> Podczas konfigurowania portu odwrotnego serwera proxy w moduł równoważenia obciążenia wszystkie mikrousługi w klastrze, które udostępniają punkt końcowy HTTP są adresowalne spoza klastra. Oznacza to, że mikrousługi przeznaczone do wewnętrznej może być wykrywalne przez określonego złośliwego użytkownika. Potencjalnie stwarza to poważne luki w zabezpieczeniach, które można wykorzystać; na przykład:
>
> * Złośliwy użytkownik może uruchomić atak typu "odmowa usługi", wielokrotnie wywołując usługę wewnętrzną, która nie ma wystarczająco wzmocnionej powierzchni ataku.
> * Złośliwy użytkownik może dostarczać nieprawidłowo sformułowane pakiety do usługi wewnętrznej, co powoduje niezamierzone zachowanie.
> * Usługa przeznaczona do wewnętrznej może zwracać prywatne lub poufne informacje, które nie są przeznaczone do narażonych na usługi poza klastrem, ujawniając w ten sposób te poufne informacje złośliwemu użytkownikowi. 
>
> Upewnij się, że w pełni zrozumieć i ograniczyć potencjalne konsekwencje zabezpieczeń dla klastra i aplikacji działających na nim, przed upublicznieniem odwrotnego portu proxy. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Format URI adresowania usług przy użyciu odwrotnego serwera proxy
Serwer proxy odwrotny używa określonego jednolitego identyfikatora zasobów (URI) format do identyfikowania partycji usługi, do której należy przesłać żądanie przychodzące:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(y):** Odwrotny serwer proxy można skonfigurować tak, aby akceptował ruch HTTP lub HTTPS. W przypadku przekazywania protokołu HTTPS zobacz [Łączenie się z bezpieczną usługą z odwrotnym serwerem proxy](service-fabric-reverseproxy-configure-secure-communication.md) po skonfigurowaniu odwrotnego serwera proxy do nasłuchiwania na stronie HTTPS.
* **W pełni kwalifikowana nazwa domeny klastra (FQDN) | wewnętrzny adres IP:** W przypadku klientów zewnętrznych można skonfigurować serwer proxy wsteczny tak, aby był dostępny za pośrednictwem domeny klastra, takiej jak mycluster.eastus.cloudapp.azure.com. Domyślnie odwrotny serwer proxy działa na każdym węźle. W przypadku ruchu wewnętrznego można uzyskać dostęp do odwrotnego serwera proxy na stronie localhost lub na dowolnym wewnętrznym adresie IP węzła, takim jak 10.0.0.1.
* **Port:** Jest to port, taki jak 19081, który został określony dla odwrotnego serwera proxy.
* **Nazwa usługi:** Jest to w pełni kwalifikowana nazwa wdrożonego wystąpienia usługi, do którego próbujesz dotrzeć bez "fabric:/" Schemat. Na przykład, aby dotrzeć do *sieci szkieletowej:/myapp/myservice/service,* należy użyć *usługi myapp/myservice*.

    W nazwie wystąpienia usługi jest rozróżniana wielkość liter. Przy użyciu innej wielkości litery dla nazwy wystąpienia usługi w adresie URL powoduje, że żądania zakończyć się niepowodzeniem z 404 (Nie znaleziono).
* **Ścieżka sufiksu:** Jest to rzeczywista ścieżka adresu URL, taka jak *myapi/values/add/3,* dla usługi, z którą chcesz się połączyć.
* **Klucz partycji:** W przypadku usługi podzielonej na partycję jest to klucz partycji obliczany partycji, do której chcesz dotrzeć. Należy zauważyć, że *nie* jest to identyfikator guid identyfikatora partycji. Ten parametr nie jest wymagany dla usług korzystających ze schematu partycji singleton.
* **PartitionKind:** Jest to schemat partycji usługi. Może to być "Int64Range" lub "Named". Ten parametr nie jest wymagany dla usług korzystających ze schematu partycji singleton.
* **Nazwa odbiornika** Punkty końcowe z usługi są w formularzu {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. Gdy usługa udostępnia wiele punktów końcowych, identyfikuje punkt końcowy, do których należy przekazać żądanie klienta. Można to pominąć, jeśli usługa ma tylko jeden odbiornik.
* **CelReplicaSelector** Określa to, jak powinna być wybrana replika docelowa lub wystąpienie.
  * Gdy usługa docelowa jest stanowa, targetReplicaSelector może być jedną z następujących czynności: "PrimaryReplica", "RandomSecondaryReplica" lub "RandomReplica". Jeśli ten parametr nie jest określony, wartość domyślna to "PrimaryReplica".
  * Gdy usługa docelowa jest bezstanowa, reverse proxy wybiera losowe wystąpienie partycji usługi do przekazania żądania do.
* **Limit czasu:**  Określa limit czasu dla żądania HTTP utworzonego przez odwrotny serwer proxy do usługi w imieniu żądania klienta. Wartość domyślna to 60 sekund. Jest to parametr opcjonalny.

### <a name="example-usage"></a>Przykład użycia
Na przykład weźmy usługę *fabric:/MyApp/MyService,* która otwiera odbiornik HTTP pod następującym adresem URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Poniżej znajdują się zasoby dla usługi:

* `/index.html`
* `/api/users/<userId>`

Jeśli usługa używa schematu partycjonowania singleton, parametry ciągu zapytania *PartitionKey* i *PartitionKind* nie są wymagane, a usługa może zostać osiągnięta przy użyciu bramy jako:

* Zewnętrznie:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Wewnętrznie:`http://localhost:19081/MyApp/MyService`

Jeśli usługa używa schematu partycjonowania Uniform Int64, parametry ciągu zapytania *PartitionKey* i *PartitionKind* muszą być używane do osiągnięcia partycji usługi:

* Zewnętrznie:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Aby dotrzeć do zasobów udostępnianych przez usługę, wystarczy umieścić ścieżkę zasobu po nazwie usługi w adresie URL:

* Zewnętrznie:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Następnie brama przekaże te żądania do adresu URL usługi:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Specjalna obsługa usług udostępniania portów
Serwer proxy sieci szkieletowej usług próbuje ponownie rozpoznać adres usługi i ponowić próbę żądania, gdy nie można uzyskać dostępu do usługi. Ogólnie rzecz biorąc, gdy nie można osiągnąć usługi, wystąpienie usługi lub replika została przeniesiona do innego węzła w ramach normalnego cyklu życia. W takim przypadku serwer proxy odwrotnej może otrzymać błąd połączenia sieciowego wskazujący, że punkt końcowy nie jest już otwarty na pierwotnie rozwiązany adres.

Jednak repliki lub wystąpienia usługi mogą współużytkować proces hosta i mogą również udostępniać port, gdy są hostowane przez serwer sieci web oparty na serwerze internetowym opartym na witrynie http.sys, w tym:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

W tej sytuacji jest prawdopodobne, że serwer sieci web jest dostępny w procesie hosta i odpowiada na żądania, ale rozwiązane wystąpienie usługi lub replika nie jest już dostępna na hoście. W takim przypadku brama otrzyma odpowiedź HTTP 404 z serwera sieci web. W związku z tym odpowiedź HTTP 404 może mieć dwa różne znaczenia:

- Przypadek #1: Adres usługi jest poprawny, ale zasób, o który żądany użytkownik nie istnieje.
- Case #2: Adres usługi jest niepoprawny, a zasób, o który żądany użytkownik może istnieć w innym węźle.

Pierwszy przypadek to zwykły HTTP 404, który jest uważany za błąd użytkownika. Jednak w drugim przypadku użytkownik zażądał zasobu, który istnieje. Odwrócony serwer proxy nie może go zlokalizować, ponieważ sama usługa została przeniesiona. Odwrotny serwer proxy musi ponownie rozwiązać adres i ponowić próbę żądania.

Odwrócony wskaźnik zastępczy potrzebuje zatem możliwości rozróżnienia między tymi dwoma przypadkami. Aby dokonać tego rozróżnienia, wymagana jest wskazówka z serwera.

* Domyślnie odwrotny serwer proxy zakłada, że sprawa #2 i próbuje rozwiązać i ponownie wydać żądanie.
* Aby wskazać przypadek #1 do odwrotnego serwera proxy, usługa powinna zwrócić następujący nagłówek odpowiedzi HTTP:

  `X-ServiceFabric : ResourceNotFound`

Ten nagłówek odpowiedzi HTTP wskazuje normalną sytuację HTTP 404, w której żądany zasób nie istnieje, a serwer proxy odwrotnej nie podejmie ponownej próby rozpoznania adresu usługi.

## <a name="special-handling-for-services-running-in-containers"></a>Specjalna obsługa usług działających w kontenerach

W przypadku usług działających wewnątrz kontenerów `Fabric_NodeIPOrFQDN` można użyć zmiennej środowiskowej, aby utworzyć [odwrotny adres URL serwera proxy,](#uri-format-for-addressing-services-by-using-the-reverse-proxy) jak w poniższym kodzie:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Dla klastra `Fabric_NodeIPOrFQDN` lokalnego jest domyślnie ustawiona na "localhost". Uruchom klaster lokalny `-UseMachineName` z parametrem, aby upewnić się, że kontenery mogą dotrzeć do odwrotnego serwera proxy uruchomionego w węźle. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego do debugowania kontenerów](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Usługi sieci szkieletowej usług, które są uruchamiane w kontenerach docker compose wymagają specjalnej *sekcji porty* docker-compose.yml http: lub https: konfiguracja. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna wdrażania docker compose w sieci szkieletowej usług Azure.](service-fabric-docker-compose.md)

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie i konfigurowanie odwrotnego serwera proxy w klastrze](service-fabric-reverseproxy-setup.md).
* [Konfigurowanie przekazywania dalej w celu zabezpieczenia usługi HTTP za pomocą odwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnozowanie zdarzeń zwrotnego serwera proxy](service-fabric-reverse-proxy-diagnostics.md)
* Zobacz przykład komunikacji HTTP między usługami w [przykładowym projekcie w usłudze GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Zdalne wywołania procedury z obsługą komunikacji zdalnej z niezawodnymi usługami](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API, który używa OWIN w niezawodnych usługach](service-fabric-reliable-services-communication-webapi.md)
* [Komunikacja WCF przy użyciu niezawodnych usług](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
