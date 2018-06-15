---
title: Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować tryb dystrybucji modułu równoważenia obciążenia Azure do obsługi koligacji IP źródła.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ae793bad9cef86158418eb87e0c38ee0370a6bd2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30176979"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurowanie trybu dystrybucji dla usługi równoważenia obciążenia Azure

## <a name="hash-based-distribution-mode"></a>Tryb dystrybucji wyznaczania wartości skrótu

Domyślny tryb dystrybucji modułu równoważenia obciążenia Azure jest skrót 5-elementowej. Źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i typ protokołu składa się spójnej kolekcji. Wartość skrótu jest używany do mapowania ruchu dostępnych serwerów i algorytm udostępnia lepkości tylko w ramach sesji transportu. Pakiety, które znajdują się w tej samej sesji są przekierowywane do tego samego wystąpienia adres IP (DIP) datacenter za punktem końcowym równoważeniem obciążenia. Przy uruchamianiu klienta w nowej sesji z tym samym źródłowy adres IP, port źródłowy zmiany i powoduje, że ruch komunikować się z innym punktem końcowym DIP.

![Tryb dystrybucji skrótu krotka 5](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Tryb koligacji IP źródła

Moduł równoważenia obciążenia można również skonfigurować przy użyciu trybu dystrybucji koligacji źródłowego adresu IP. W tym trybie dystrybucji jest nazywana koligacji sesji lub koligacji IP klienta. Krotka 2 (źródłowy adres IP i docelowy adres IP) używa trybu lub krotki 3 (typ źródłowy adres IP, docelowy adres IP i protokołu) wyznaczania wartości skrótu do mapowania ruchu na dostępne serwery. Za pomocą koligacji IP źródła, połączeń, które są inicjowane z tego samego komputera klienckiego przejdź do tego samego punktu końcowego DIP.

Poniższa ilustracja przedstawia konfigurację podwójnego 2. Zwróć uwagę, sposób uruchamiania krotki 2 za pośrednictwem usługi równoważenia obciążenia do maszyny wirtualnej 1 (VM1). VM1 jest następnie kopii zapasowej maszyny VM2 i VM3.

![Tryb dystrybucji koligacji sesji krotki 2](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Tryb koligacji IP źródła rozwiązuje występuje niezgodność między modułu równoważenia obciążenia Azure oraz bramy usług pulpitu zdalnego (RD Gateway). Przez ten tryb jest używany, można utworzyć farmę serwerów bramy usług pulpitu zdalnego w usłudze w chmurze pojedynczego.

Inny scenariusz użycia jest przekazywanie nośnika. Przekazywanie danych odbywa się w ramach protokołu UDP, ale płaszczyzny sterowania odbywa się za pośrednictwem protokołu TCP:

* Klient inicjuje sesji TCP do publicznego adresu równoważeniem obciążenia i jest kierowane do określonego adresu DIP. Kanał pozostaje aktywna do monitorowania kondycji połączenia.
* Nowa sesja UDP z tego samego komputera klienckiego jest inicjowany do tego samego równoważeniem obciążenia publicznego punktu końcowego. Połączenie jest kierowane do tego samego punktu końcowego DIP jako poprzednie połączenie TCP. Przekazywanie nośnika mogą być wykonywane w wysokiej przepływności przy zachowaniu kanału kontroli, za pośrednictwem protokołu TCP.

> [!NOTE]
> Zmiana zestawu z równoważeniem obciążenia przez usunięcie lub dodawania maszyny wirtualnej jest przeliczane dystrybucji żądań klientów. Nie są zależne od nowych połączeń z istniejących klientów na końcu tego samego serwera. Ponadto przy użyciu źródłowy adres IP trybie dystrybucji koligacji może spowodować nierówne rozkładu ruchu. Klienci, którzy Uruchom tyłu proxy może być widoczny jako jedną aplikację unikatowych klientów.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurowanie ustawień koligacji IP źródła

W przypadku maszyn wirtualnych przy użyciu programu Azure PowerShell zmienić ustawienia limitu czasu. Dodawanie punktu końcowego platformy Azure do maszyny wirtualnej i skonfiguruj tryb dystrybucji modułu równoważenia obciążenia:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ustaw wartość `LoadBalancerDistribution` elementu potrzebną ilość równoważenia obciążenia. Określ sourceIP Równoważenie obciążenia sieciowego 2 parametrów (źródłowy adres IP i docelowy adres IP). Określ sourceIPProtocol dla 3 parametrów (źródłowy adres IP, docelowy adres IP i protokół typu) funkcji równoważenia obciążenia. Określ Brak dla domyślnego zachowania 5-elementowej Równoważenie obciążenia sieciowego.

Pobieranie konfiguracji trybu dystrybucji modułu równoważenia obciążenia punktu końcowego przy użyciu tych ustawień:

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

Gdy `LoadBalancerDistribution` element nie jest obecny, moduł równoważenia obciążenia Azure używa domyślnego algorytmu 5-elementowej.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurowanie trybu dystrybucji na zestawu punktów końcowych z równoważeniem obciążenia

Gdy punkty końcowe są częścią zestawu o zrównoważonym obciążeniu punktu końcowego, tryb dystrybucji musi być skonfigurowany w zestawie równoważeniem obciążenia punktu końcowego:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurowanie trybu dystrybucji dla punktów końcowych usługi w chmurze

Zestaw Azure SDK for .NET 2.5 należy używać do aktualizowania usługi w chmurze. Ustawienia punktu końcowego usługi w chmurze są nawiązywane w pliku csdef. Aby zaktualizować trybu dystrybucji modułu równoważenia obciążenia dla wdrożenia usługi w chmurze, wymagany jest uaktualniania wdrożenia.

Oto przykład csdef zmiany ustawień punktu końcowego:

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

## <a name="api-example"></a>Przykładowy interfejs API

Poniższy przykład pokazuje, jak zmienić konfigurację trybu dystrybucji modułu równoważenia obciążenia dla określonego zestawu z równoważeniem obciążenia we wdrożeniu. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Zmień tryb dystrybucji dla wdrożonych zestawu z równoważeniem obciążenia

Umożliwia zmianę istniejącą konfigurację wdrożenia Azure klasycznym modelu wdrażania. Dodaj `x-ms-version` nagłówka i ustawia wartość do wersji 2014-09-01 lub nowszym.

#### <a name="request"></a>Żądanie

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

Opisane wcześniej, ustaw `LoadBalancerDistribution` elementu sourceIP koligacji krotki 2, sourceIPProtocol koligacji krotki 3 lub brak nie koligacji (5-elementowej koligacja).

#### <a name="response"></a>Odpowiedź

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure wewnętrzny moduł równoważenia obciążenia](load-balancer-internal-overview.md)
* [Wprowadzenie do konfigurowania usługi równoważenia obciążenia połączonych z Internetem](load-balancer-get-started-internet-arm-ps.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
