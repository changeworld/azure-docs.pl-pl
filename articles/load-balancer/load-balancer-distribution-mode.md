---
title: Konfigurowanie trybu dystrybucji modułu Równoważenia obciążenia platformy Azure
titleSuffix: Azure Load Balancer
description: W tym artykule rozpocznij konfigurowanie trybu dystrybucji dla modułu Azure Load Balancer do obsługi źródłowej koligacji adresów IP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023535"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurowanie trybu dystrybucji dla modułu równoważenia obciążenia platformy Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Tryb dystrybucji oparty na mieszanie

Domyślny tryb dystrybucji dla usługi Azure Load Balancer to skrót krotki z pięcioma elementami. 

Krotka składa się z:
* **Źródłowy adres IP**
* **Port źródłowy**
* **Docelowy adres IP**
* **Port docelowy**
* **Typ protokołu**

Skrót służy do mapowania ruchu na dostępne serwery. Algorytm zapewnia lepkość tylko w ramach sesji transportu. Pakiety, które znajdują się w tej samej sesji są kierowane do tego samego adresu IP centrum danych za punktem końcowym równoważenia obciążenia. Gdy klient uruchamia nową sesję z tego samego źródłowego adresu IP, port źródłowy zmienia się i powoduje, że ruch przechodzi do innego punktu końcowego centrum danych.

![Tryb dystrybucji oparty na pięcioplełkowym trybie dystrybucji](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Źródłowy tryb koligacji IP

Moduł równoważenia obciążenia można również skonfigurować przy użyciu źródłowego trybu dystrybucji koligacji IP. Ten tryb dystrybucji jest również znany jako koligacja sesji lub koligacja adresów IP klienta. W trybie użyto skrótu dwutą warstwy (źródłowego adresu IP i docelowego adresu IP) lub trzy krotki (źródłowy adres IP, docelowy adres IP i typ protokołu) do mapowania ruchu na dostępne serwery. Przy użyciu koligacji źródłowej ip, połączenia, które są uruchamiane z tego samego komputera klienckiego przejść do tego samego punktu końcowego centrum danych.

Na poniższym rysunku przedstawiono konfigurację dwóch krotek. Zwróć uwagę, jak dwupletka przebiega za pośrednictwem modułu równoważenia obciążenia do maszyny wirtualnej 1 (VM1). Maszyna wirtualna1 jest następnie wspierana przez maszyny VM2 i VM3.

![Tryb dystrybucji koligacji sesji z dwiema krotami](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Źródłowy tryb koligacji IP rozwiązuje niezgodność między modułem równoważenia obciążenia platformy Azure a bramą pulpitu zdalnego (brama usług pulpitu zdalnego). Korzystając z tego trybu, można utworzyć farmę bramy usług pulpitu zdalnego w jednej usłudze w chmurze.

Innym scenariuszem przypadku użycia jest przekazywanie multimediów. Przekazywanie danych odbywa się za pośrednictwem protokołu UDP, ale płaszczyzna sterowania jest osiągana za pośrednictwem protokołu TCP:

* Klient uruchamia sesję TCP do adresu publicznego z równoważenia obciążenia i jest kierowany do określonego DIP. Kanał pozostaje aktywny do monitorowania kondycji połączenia.
* Nowa sesja UDP z tego samego komputera klienckiego jest uruchamiana do tego samego publicznego punktu końcowego z równoważeniem obciążenia. Połączenie jest kierowane do tego samego punktu końcowego DIP co poprzednie połączenie TCP. Przekazywanie multimediów może być wykonywane z dużą przepustowością przy zachowaniu kanału sterowania za pośrednictwem protokołu TCP.

> [!NOTE]
> Gdy zestaw z równoważeniem obciążenia zmienia się przez usunięcie lub dodanie maszyny wirtualnej, dystrybucja żądań klientów jest ponownie obliczana. Nie można polegać na nowych połączeniach z istniejących klientów, aby skończyć na tym samym serwerze. Ponadto przy użyciu źródłowego trybu dystrybucji koligacji IP może spowodować nierówny rozkład ruchu. Klienci, którzy działają za serwerami proxy może być postrzegana jako jedna unikatowa aplikacja kliencka.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurowanie źródłowych ustawień koligacji IP

### <a name="azure-portal"></a>Portal Azure

Konfigurację trybu dystrybucji można zmienić, modyfikując regułę równoważenia obciążenia w portalu.

1. Zaloguj się do witryny Azure portal i znajdź grupę zasobów zawierającą moduł równoważenia obciążenia, który chcesz zmienić, klikając pozycję **Grupy zasobów**.
2. Na ekranie przeglądu modułu równoważenia obciążenia kliknij pozycję **Reguły równoważenia obciążenia** w obszarze **Ustawienia**.
3. Na ekranie reguł równoważenia obciążenia kliknij regułę równoważenia obciążenia, która ma zmienić tryb dystrybucji.
4. Zgodnie z regułą tryb dystrybucji jest zmieniany przez zmianę pola **rozwijane Trwałość sesji.**  Dostępne są następujące opcje:
    
    * **Brak (oparte na skrótach)** — określa, że kolejne żądania od tego samego klienta mogą być obsługiwane przez dowolną maszynę wirtualną.
    * **Adres IP klienta (źródłona koligacja IP 2-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną.
    * **Adres IP klienta i protokół (źródłowa koligacja IP 3-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta i kombinacji protokołu będą obsługiwane przez tę samą maszynę wirtualną.

5. Wybierz tryb dystrybucji, a następnie kliknij przycisk **Zapisz**.

### <a name="azure-powershell"></a>Azure PowerShell

W przypadku maszyn wirtualnych wdrożonych za pomocą Menedżera zasobów użyj programu PowerShell, aby zmienić ustawienia dystrybucji modułu równoważenia obciążenia w istniejącej regule równoważenia obciążenia. Następujące polecenie aktualizuje tryb dystrybucji: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

W przypadku klasycznych maszyn wirtualnych należy zmienić ustawienia dystrybucji za pomocą programu Azure PowerShell. Dodaj punkt końcowy platformy Azure do maszyny wirtualnej i skonfiguruj tryb dystrybucji modułu równoważenia obciążenia:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ustaw wartość `LoadBalancerDistribution` elementu dla wymaganego równoważenia obciążenia. Określ sourceIP dla równoważenia obciążenia dwutłąką (źródłowy adres IP i docelowy adres IP). Określ sourceIPProtocol dla równoważenia obciążenia z trzema krotkami (źródłowy adres IP, docelowy adres IP i typ protokołu). Określ brak domyślnego zachowania równoważenia obciążenia z pięcioma krotami.

Pobieranie konfiguracji trybu dystrybucji modułu równoważenia obciążenia punktu końcowego przy użyciu następujących ustawień:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Gdy `LoadBalancerDistribution` element nie jest obecny, Azure Load Balancer używa domyślnego algorytmu pięciu krotek.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurowanie trybu dystrybucji w zestawie punktów końcowych z równoważeniem obciążenia

Gdy punkty końcowe są częścią zestawu punktów końcowych z równoważeniem obciążenia, tryb dystrybucji musi być skonfigurowany w zestawie punktów końcowych z równoważeniem obciążenia:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurowanie trybu dystrybucji dla punktów końcowych usług w chmurze

Użyj pakietu Azure SDK dla platformy .NET 2.5, aby zaktualizować usługę w chmurze. Ustawienia punktu końcowego dla usług w chmurze są dokonywane w pliku csdef. Aby zaktualizować tryb dystrybucji modułu równoważenia obciążenia dla wdrożenia usług w chmurze, wymagane jest uaktualnienie wdrażania.

Oto przykład zmian .csdef dla ustawień punktu końcowego:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Przykład interfejsu API

W poniższym przykładzie pokazano, jak ponownie skonfigurować tryb dystrybucji modułu równoważenia obciążenia dla określonego zestawu z równoważeniem obciążenia we wdrożeniu. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Zmień tryb dystrybucji dla wdrożonego zestawu z równoważenia obciążenia

Użyj klasycznego modelu wdrażania platformy Azure, aby zmienić istniejącą konfigurację wdrożenia. Dodaj `x-ms-version` nagłówek i ustaw wartość na wersję 2014-09-01 lub nowszą.

#### <a name="request"></a>Żądanie

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Jak opisano wcześniej, ustaw `LoadBalancerDistribution` element sourceIP dla koligacji dwóch krotek, sourceIPProtocol dla koligacji z trzema krotkami lub brak dla braku koligacji (koligacja z pięcioma krotkami).

#### <a name="response"></a>Odpowiedź

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Następne kroki

* [Omówienie wewnętrznego modułu równoważenia obciążenia platformy Azure](load-balancer-internal-overview.md)
* [Wprowadzenie do konfigurowania internetowego modułu równoważenia obciążenia](quickstart-create-standard-load-balancer-powershell.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
