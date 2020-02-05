---
title: Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia platformy Azure
titleSuffix: Azure Load Balancer
description: W tym artykule Rozpocznij Konfigurowanie trybu dystrybucji dla Azure Load Balancer w celu obsługi koligacji źródłowego adresu IP.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023535"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Tryb dystrybucji skrótów

Domyślny tryb dystrybucji dla Azure Load Balancer jest skrótem pięciu krotek. 

Krotka składa się z:
* **Źródłowy adres IP**
* **Port źródłowy**
* **Docelowy adres IP**
* **Port docelowy**
* **Typ protokołu**

Skrót jest używany do mapowania ruchu do dostępnych serwerów. Algorytm zawiera lepkość tylko w ramach sesji transportu. Pakiety znajdujące się w tej samej sesji są kierowane do tego samego adresu IP centrum danych za punktem końcowym ze zrównoważonym obciążeniem. Gdy klient uruchamia nową sesję z tego samego źródłowego adresu IP, port źródłowy zmieni się i spowoduje, że ruch przejdzie do innego punktu końcowego centrum danych.

![Tryb dystrybucji oparty na skrócie pięciu krotek](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Tryb koligacji IP źródła

Moduł równoważenia obciążenia można również skonfigurować za pomocą trybu dystrybucji koligacji źródłowego adresu IP. W tym trybie dystrybucji jest również nazywany koligacja sesji lub koligacja adresu IP klienta. Tryb używa skrótu z dwiema kolekcjami (źródłowy adres IP i docelowy adres IP) lub z trzema kolekcjami (źródłowy adres IP, docelowy adres IP i typ protokołu), aby mapować ruch do dostępnych serwerów. Korzystając z koligacji źródłowego adresu IP, połączenia uruchamiane z tego samego komputera klienckiego są przekazywane do tego samego punktu końcowego centrum danych.

Na poniższej ilustracji przedstawiono konfigurację z dwoma spójnymi kolekcjami. Zauważ, że dwie kolekcje są uruchamiane za pomocą modułu równoważenia obciążenia do maszyny wirtualnej 1 (VM1). Maszyna VM1 następnie kopia zapasowa jest tworzona maszyna VM2 i VM3.

![Tryb dystrybucji koligacji sesji dwóch krotek](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Tryb koligacji IP źródła rozwiązuje niezgodności między równoważenia obciążenia platformy Azure i bramy usług pulpitu zdalnego (RD Gateway). Korzystając z tego trybu, można utworzyć farmę serwerów usługi Brama usług pulpitu zdalnego w usłudze w chmurze.

Inny scenariusz przypadków użycia jest przekazywanie nośników. Przekazywanie danych odbywają się za pośrednictwem protokołu UDP, ale na płaszczyźnie kontroli odbywa się za pośrednictwem protokołu TCP:

* Klient uruchamia sesję TCP na adres publiczny ze zrównoważonym obciążeniem i jest kierowany do określonego DIP. Kanał pozostaje aktywna w celu monitorowania kondycji połączenia.
* Nowa sesja UDP z tego samego komputera klienckiego jest uruchamiana w tym samym publicznym punkcie końcowym ze zrównoważonym obciążeniem. Połączenie jest kierowany do tego samego punktu końcowego adresu DIP, ponieważ poprzednie połączenie TCP. Przekazywanie nośników mogą być wykonywane przy wysokiej przepływności, przy zachowaniu kanał kontrolny, za pośrednictwem protokołu TCP.

> [!NOTE]
> Zmiana zestawu z równoważeniem obciążenia przez usunięcie lub dodanie maszyny wirtualnej jest ponownie przeliczana dystrybucji żądań klientów. Użytkownik nie może zależeć od nowych połączeń z istniejących klientów do kończy się na tym samym serwerze. Ponadto za pomocą źródłowego adresu IP trybu dystrybucji koligacji może spowodować nierówne dystrybucji ruchu. Klientów z systemami za serwery proxy może być traktowany jako jeden wniosek unikatowych klientów.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurowanie ustawień koligacji IP źródła

### <a name="azure-portal"></a>Portal Azure

Konfigurację trybu dystrybucji można zmienić, modyfikując regułę równoważenia obciążenia w portalu.

1. Zaloguj się do Azure Portal i Znajdź grupę zasobów zawierającą moduł równoważenia obciążenia, który chcesz zmienić, klikając pozycję **grupy zasobów**.
2. Na ekranie przegląd modułu równoważenia obciążenia kliknij pozycję **reguły równoważenia obciążenia** w obszarze **Ustawienia**.
3. Na ekranie reguły równoważenia obciążenia kliknij regułę równoważenia obciążenia, w której chcesz zmienić tryb dystrybucji.
4. W ramach zasady tryb dystrybucji jest zmieniany, zmieniając pole listy rozwijanej **trwałość sesji** .  Dostępne są następujące opcje:
    
    * **Brak (oparte na skrócie)** — określa, że kolejne żądania z tego samego klienta mogą być obsługiwane przez dowolną maszynę wirtualną.
    * **Adres IP klienta (koligacja źródłowego adresu IP 2-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną.
    * **IP i protokół klienta (koligacja źródłowego adresu IP 3-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta i kombinacji protokołów będą obsługiwane przez tę samą maszynę wirtualną.

5. Wybierz tryb dystrybucji, a następnie kliknij przycisk **Zapisz**.

### <a name="azure-powershell"></a>Program Azure PowerShell

W przypadku maszyn wirtualnych wdrożonych przy użyciu Menedżer zasobów należy użyć programu PowerShell, aby zmienić ustawienia dystrybucji modułu równoważenia obciążenia na istniejącą regułę równoważenia obciążenia. Następujące polecenie aktualizuje tryb dystrybucji: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Dla klasycznych maszyn wirtualnych zmieniać ustawienia dystrybucji za pomocą programu Azure PowerShell. Dodawanie punktu końcowego platformy Azure do maszyny wirtualnej i Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ustaw wartość `LoadBalancerDistribution` elementu dla wymaganej ilości usługi równoważenia obciążenia. Określ sourceIP dla dwóch krotek (źródłowy adres IP i docelowy adres IP). Określ sourceIPProtocol dla funkcji równoważenia obciążenia dla trzech krotek (źródłowy adres IP, docelowy adres IP i typ protokołu). Określ brak dla domyślnego zachowania dla pięciu krotek równoważenia obciążenia.

Pobierz konfigurację trybu dystrybucji modułu równoważenia punktu końcowego obciążenia za pomocą tych ustawień:

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

Gdy element `LoadBalancerDistribution` nie istnieje, Azure Load Balancer używa domyślnego algorytmu pięciu krotek.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurowanie trybu dystrybucji na zestaw punktów końcowych z równoważeniem obciążenia

Punkty końcowe są częścią zestawu końcowy z równoważeniem obciążenia, tryb dystrybucji muszą zostać skonfigurowane w zestawie końcowy z równoważeniem obciążenia:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurowanie trybu dystrybucji dla punktów końcowych usługi w chmurze

Aby zaktualizować usługę w chmurze, należy użyć zestawu Azure SDK dla platformy .NET 2.5. Ustawienia punktu końcowego usług w chmurze są dokonywane w pliku csdef. Aby zaktualizować trybu dystrybucji modułu równoważenia obciążenia dla wdrożenia usług Cloud Services, uaktualnianie wdrożenia jest wymagany.

Oto przykład .csdef zmiany ustawień punktu końcowego:

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

Poniższy przykład pokazuje, jak zmienić konfigurację trybu dystrybucji modułu równoważenia obciążenia dla określonego zestawu z równoważeniem obciążenia w danym wdrożeniu. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Zmień tryb dystrybucji dla wdrożonej zrównoważonym obciążeniu

Aby zmienić istniejącą konfigurację wdrożenia przy użyciu modelu klasycznym wdrożeniu platformy Azure. Dodaj `x-ms-version` nagłówek i ustawia wartość do wersji 2014-09-01 lub nowszej.

#### <a name="request"></a>Prośba

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

Jak opisano wcześniej, ustaw `LoadBalancerDistribution` element sourceIP dla koligacji dwóch krotek, sourceIPProtocol dla koligacji z trzema krotkami lub brak koligacji (koligacja z pięcioma krotkami).

#### <a name="response"></a>Odpowiedź

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Następne kroki

* [Omówienie wewnętrznego modułu równoważenia obciążenia na platformie Azure](load-balancer-internal-overview.md)
* [Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](quickstart-create-standard-load-balancer-powershell.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
