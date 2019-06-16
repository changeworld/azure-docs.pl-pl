---
title: Usługa Azure Service Fabric zwrotny serwer proxy | Dokumentacja firmy Microsoft
description: Zwrotny serwer proxy usługi Service Fabric na użytek komunikacji z mikrousług z i spoza klastra.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 6ce6f1f6559b43a64fb7edd0773a20f8ee0cf8a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837967"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Zwrotny serwer proxy w usłudze Azure Service Fabric
Zwrotny serwer proxy, wbudowana w usłudze Azure Service Fabric pomaga mikrousług działającego w klastrze usługi Service Fabric, odnajdywanie i komunikować się z innymi usługami, które mają punktów końcowych http.

## <a name="microservices-communication-model"></a>Model komunikacji Mikrousług
Mikrousługi w usłudze Service Fabric Uruchom dla podzbioru węzłów w klastrze i jest Migrowanie między węzłami z różnych powodów. W rezultacie punkty końcowe dla mikrousług można dynamicznie zmieniać. Aby odnaleźć i komunikować się z innymi usługami w klastrze, mikrousługi musi przejść przez następujące kroki:

1. Ustala lokalizację usługi, za pośrednictwem usługi nazewnictwa.
2. Połączenia z usługą.
3. OPAKOWYWANIE poprzednie kroki w pętli, który implementuje usługę rozpoznawania, a następnie spróbuj ponownie zasady do zastosowania na błędy połączenia

Aby uzyskać więcej informacji, zobacz [Connect i komunikować się z usługami](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Komunikacja przy użyciu zwrotnego serwera proxy
Zwrotny serwer proxy to usługa, która działa w każdym węźle i obsługuje punkt końcowy rozpoznawania, automatyczny i inne błędy połączeń w imieniu usługi klienta. Zwrotny serwer proxy można skonfigurować do zastosowania różnych zasad, ponieważ obsługuje on żądania z klienta usług. Przy użyciu zwrotnego serwera proxy umożliwia usługę klienta korzystać z żadnych bibliotek komunikacji HTTP po stronie klienta i nie wymagają specjalnych rozwiązania i Logika ponawiania w usłudze. 

Zwrotny serwer proxy przedstawia jeden lub więcej punktów końcowych na lokalny węzeł usługi klienta używany do wysyłania żądań do innych usług.

![Wewnętrznej komunikacji][1]

> [!NOTE]
> **Obsługiwane platformy**
>
> Zwrotny serwer proxy w usłudze Service Fabric aktualnie obsługuje następujące platformy
> * *Klaster Windows*: System Windows 8 lub nowszym lub Windows Server 2012 i nowsze
> * *Klaster systemu Linux*: Zwrotny serwer Proxy nie jest obecnie dostępna w przypadku klastrów systemu Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Docieranie do mikrousług z poza klastrem
Domyślny model łączności zewnętrznej dla mikrousług jest model opt-in, gdzie każdej z tych usług nie są dostępne bezpośrednio z klientami zewnętrznymi. [Usługa Azure Load Balancer](../load-balancer/load-balancer-overview.md), czyli granicę sieci między mikrousług i zewnętrznych klientów, wykonuje translatora adresów sieciowych i przekazuje zewnętrznych żądania z punktami końcowymi IP:port wewnętrznego. Aby udostępnić punkt końcowy mikrousług bezpośrednio dla klientów zewnętrznych, należy najpierw skonfigurować moduł równoważenia obciążenia do przesyłania ruchu do każdy port używany przez usługę w klastrze. Ponadto większość mikrousług, szczególnie mikrousług stanowych, nie znajdują się we wszystkich węzłach klastra. Mikrousługi można przenosić między węzłami w trybie failover. W takich przypadkach modułu równoważenia obciążenia nie może skutecznie określenia lokalizacji węzeł docelowy replik, do których należy przekazywać ruch.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Docieranie do mikrousług przy użyciu zwrotnego serwera proxy spoza klastra
Zamiast konfigurować portu poszczególnych usług w module równoważenia obciążenia, można skonfigurować tylko port zwrotnego serwera proxy w module równoważenia obciążenia. Ta konfiguracja pozwala klientom spoza klastra dotrzeć do usług w klastrze przy użyciu zwrotnego serwera proxy bez dodatkowej konfiguracji.

![Zewnętrzne komunikacji][0]

> [!WARNING]
> Po skonfigurowaniu port zwrotnego serwera proxy w module równoważenia obciążenia, wszystkie mikrousług w klastrze, które ujawniają punkt końcowy HTTP adresowane z poza klastrem. Oznacza to, że mikrousług należy traktować jako wewnętrzny mogą być wykrywalny przez złośliwego użytkownika określone. Potencjalnie stwarza poważne luki w zabezpieczeniach, które mogą być wykorzystane; na przykład:
>
> * Złośliwy użytkownik będą mogły uruchamiać wielokrotnie, wywołując wewnętrzna usługa, która nie ma wystarczająco ataku typu "odmowa usługi".
> * Złośliwy użytkownik może dostarczyć źle sformułowane pakiety do wewnętrzna usługa skutkuje niezamierzone zachowanie.
> * Usługi, należy traktować jako wewnętrzny mogą zwracać prywatne lub poufne informacje, nie mają być udostępniana dla usług poza klastrem, w związku z tym udostępnianie danych poufnych na złośliwy użytkownik. 
>
> Upewnij się, w pełni zrozumieć i eliminowanie potencjalnych zagadnienia zabezpieczeń dla klastra i aplikacje działające na, zanim można upublicznić port zwrotnego serwera proxy. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Format identyfikatora URI do adresowania usług przy użyciu zwrotnego serwera proxy
Zwrotny serwer proxy w formacie określonym uniform resource identifier identyfikator (URI) do identyfikowania partycji usługi, do której należy przesłać żądanie przychodzące:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** Zwrotny serwer proxy można skonfigurować do akceptowania ruchu HTTP lub HTTPS. Przekazywanie protokołu HTTPS można znaleźć [nawiązywanie połączenia z bezpiecznej usłudze przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md) po instalacji zwrotny serwer proxy do nasłuchiwania przy użyciu protokołu HTTPS.
* **Klaster w pełni kwalifikowaną nazwę domeny (FQDN) | wewnętrzny adres IP:** Dla klientów zewnętrznych zwrotny serwer proxy można skonfigurować, tak aby był dostępny za pośrednictwem domeny klastra, takie jak mycluster.eastus.cloudapp.azure.com. Domyślnie zwrotny serwer proxy jest uruchamiane w każdym węźle. W przypadku ruchu wewnętrznego zwrotny serwer proxy jest osiągalna na hoście lokalnym lub na dowolny adres IP węzła wewnętrznego, np. 10.0.0.1.
* **Port:** Jest to port, np. 19081, który został określony dla zwrotnego serwera proxy.
* **ServiceInstanceName:** Jest to w pełni kwalifikowana nazwa wystąpienia wdrożonej usługi, z którym próbujesz nawiązać połączenie bez "Service fabric: /" schematu. Na przykład, aby osiągnąć *Service fabric: / myapp/Moja_usługa/* usługi, należy użyć *myapp/Moja_usługa*.

    Nazwa wystąpienia usługi jest rozróżniana wielkość liter. Przy użyciu innej wielkości liter dla nazwy wystąpienia usługi w adresie URL powoduje, że żądania z 404 (nie znaleziono).
* **Sufiks ścieżki:** Jest rzeczywista Ścieżka adresu URL, taki jak *myapi/wartości/Dodaj/3*, usługi, którą chcesz nawiązać połączenie.
* **PartitionKey:** Dla podzielonej na partycje usługi to klucz partycji obliczanej partycji, który chcesz się połączyć. Należy zauważyć, że jest to *nie* partycji Identyfikatora GUID. Ten parametr nie jest wymagane dla usług, które używają schematu partycji pojedynczego wystąpienia.
* **PartitionKind:** Jest to schemat partycji usługi. Może to być "Int64Range" lub "Named". Ten parametr nie jest wymagane dla usług, które używają schematu partycji pojedynczego wystąpienia.
* **ListenerName** punkty końcowe usługi są w formie {"Punkty końcowe": {"Listener1": "Punk końcowy 1", "Listener2": "Endpoint2" …}}. Gdy usługa udostępnia wiele punktów końcowych, identyfikuje punkt końcowy, który żądanie klienta powinien być przekazywany do. To można pominąć, jeśli usługa ma tylko jeden odbiornik.
* **TargetReplicaSelector** Określa, jak należy wybrać docelowej repliki lub wystąpienia.
  * Gdy Usługa docelowa jest stanową, TargetReplicaSelector może być jedną z następujących czynności:  "PrimaryReplica", "RandomSecondaryReplica" lub "RandomReplica". Jeśli ten parametr nie jest określony, wartością domyślną jest "PrimaryReplica".
  * Po bezstanowa Usługa docelowa zwrotny serwer proxy wybiera losową wystąpienia partycji usługi w celu przesłania żądania do.
* **Limit czasu:**  To ustawienie określa limit czasu żądania HTTP, utworzone przez zwrotny serwer proxy do usługi w imieniu żądania klienta. Wartość domyślna to 60 sekund. Jest to parametr opcjonalny.

### <a name="example-usage"></a>Przykład użycia
Na przykład Weźmy *Service fabric: / MyApp/Moja_usługa* usługa, która otwiera odbiornika protokołu HTTP na następujący adres URL:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Poniżej przedstawiono zasoby usługi:

* `/index.html`
* `/api/users/<userId>`

Jeśli usługa używa pojedynczego schematu partycjonowania *PartitionKey* i *PartitionKind* parametry ciągu zapytania nie są wymagane, a usługa jest osiągalna przy użyciu bramy jako:

* Zewnętrzne: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Wewnętrznie: `http://localhost:19081/MyApp/MyService`

Jeśli usługa używa schematu partycjonowania jednolitego Int64 *PartitionKey* i *PartitionKind* parametry ciągu zapytania należy użyć, aby dotrzeć do partycji usługi:

* Zewnętrzne: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Dostęp do zasobów, które uwidacznia usługa, po prostu umieść ścieżka zasobu po nazwie usługi w adresie URL:

* Zewnętrzne: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Wewnętrznie: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Brama będzie przesyłania dalej tych żądań do adresu URL usługi:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Specjalna obsługa współużytkowania portów usług
Zwrotny serwer proxy usługi Service Fabric próbuje rozpoznać adres usługi ponownie i ponów żądanie, gdy nie można nawiązać połączenia usługi. Ogólnie rzecz biorąc gdy nie można połączyć się z usługą, wystąpienie usługi lub repliki został przeniesiony do innego węzła jako część jej cyklu projektowania normalny. W takim przypadku zwrotny serwer proxy może zostać wyświetlony błąd połączenia sieciowego, wskazujący, że punkt końcowy nie jest już otwarty na pierwotnie rozpoznany adres.

Jednak replik lub wystąpień usługi można udostępniać procesu hosta i może także udostępnić obsługiwanej przez serwer sieci web opartych na http.sys port w tym:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

W tej sytuacji jest serwer sieci web jest dostępna w procesie hosta i odpowiadanie na żądania, że wystąpienie usługi rozwiązany lub replika nie jest już dostępny na hoście. W tym przypadku bramy z serwera sieci web otrzyma komunikat odpowiedzi HTTP 404. W związku z tym komunikat odpowiedzi HTTP 404 może mieć dwie różne znaczenie:

- Przypadek #1: Adres usługi jest poprawny, ale zasób, który użytkownik zażądał nie istnieje.
- Przypadek #2: Adres usługi jest nieprawidłowa, a zasób, który użytkownik zażądał może istnieć w innym węźle.

Pierwszy przypadek jest normalne 404 protokołu HTTP, który jest uważany za błąd użytkownika. Jednak w drugim przypadku użytkownik zażądał zasobem, który istnieje. Zwrotny serwer proxy nie może go znaleźć, ponieważ sama usługa została przeniesiona. Zwrotny serwer proxy musi rozpoznać adresu ponownie i ponów próbę żądania.

Zwrotny serwer proxy w związku z tym musi mieć możliwość rozróżnienie tych dwóch przypadkach. Aby tego rozróżnienia, wskazówkę z serwera jest wymagany.

* Domyślnie zwrotny serwer proxy zakłada przypadek #2 i próbuje rozpoznać i ponownie wysłać żądanie.
* Aby wskazać, w przypadku #1 do zwrotnego serwera proxy, usługa powinna zwrócić następujące nagłówka odpowiedzi HTTP:

  `X-ServiceFabric : ResourceNotFound`

Ten nagłówek odpowiedzi HTTP wskazuje normalnej sytuacji HTTP 404, w której żądany zasób nie istnieje, a zwrotny serwer proxy nie będzie próbował rozpoznać adres usługi ponownie.

## <a name="special-handling-for-services-running-in-containers"></a>Specjalna obsługa usług działających w kontenerach

W przypadku usług działających w kontenerach, można użyć zmiennej środowiskowej `Fabric_NodeIPOrFQDN` do konstruowania [Wycofaj adres URL serwera proxy](#uri-format-for-addressing-services-by-using-the-reverse-proxy) zgodnie z poniższym kodem:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Dla klastra lokalnego `Fabric_NodeIPOrFQDN` jest domyślnie do "localhost". Uruchom lokalny klaster przy użyciu `-UseMachineName` parametru, aby upewnić się, kontenerów może osiągnąć zwrotny serwer proxy, uruchomione w węźle. Aby uzyskać więcej informacji, zobacz [konfigurowania środowiska dewelopera w celu debugowania kontenery](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Usługi Service Fabric, działających w kontenerach Docker Compose wymagają specjalnych docker-compose.yml *porty sekcji* http: lub https: konfiguracji. Aby uzyskać więcej informacji, zobacz [narzędzia Docker Compose Obsługa wdrażania w usłudze Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie i konfigurowanie zwrotnego serwera proxy w klastrze](service-fabric-reverseproxy-setup.md).
* [Konfigurowanie funkcji przekazywania danych do bezpiecznej usługi HTTP przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnozowanie zdarzenia zwrotny serwer proxy](service-fabric-reverse-proxy-diagnostics.md)
* Zobacz przykład protokołu HTTP do komunikacji między usługami w [przykładowy projekt w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Zdalne wywołania procedur z wywołaniem funkcji zdalnych usług Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API, który używa OWIN usług Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Komunikacji WCF przy użyciu usług Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
