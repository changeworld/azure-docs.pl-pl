---
title: Konfigurowanie limitu czasu bezczynności protokołu TCP modułu równoważenia obciążenia na platformie Azure
titlesuffix: Azure Load Balancer
description: Konfigurowanie limitu czasu bezczynności protokołu TCP modułu równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: kumudd
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0c57eec4d739da13d98099a6b2f01fbf0ad0051c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734608"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Skonfiguruj ustawienia limitu czasu bezczynności TCP dla modułu równoważenia obciążenia platformy Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

W konfiguracji domyślnej usługa Azure Load Balancer ma ustawienie limitu czasu bezczynności trwający 4 min. Jeśli w okresie braku aktywności jest dłuższa niż wartość limitu czasu, nie ma gwarancji, TCP lub HTTP w sesji jest utrzymywany między klientem i usługi w chmurze.

Gdy połączenie jest zamknięte, Twoja aplikacja kliencka może zostać wyświetlony następujący komunikat o błędzie: "Połączenie podstawowe zostało zamknięte: Oczekiwano życiu połączenie zostało zamknięte przez serwer."

Powszechną praktyką jest, aby użyć TCP keep-alive. Praktyka ta przechowuje połączenia aktywne przez dłuższy czas. Aby uzyskać więcej informacji, zobacz te [przykłady .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Włączone keep-alive, pakiety są wysyłane w czasie nieaktywności w połączeniu. Te pakiety utrzymywania aktywności upewnij się, że wartości limitu czasu bezczynności nigdy nie zostanie osiągnięty, a połączenie jest utrzymywane przez długi czas.

To ustawienie działa tylko w przypadku połączeń przychodzących. Aby uniknąć utraty połączenia, możesz skonfigurować keep-alive TCP w odstępie czasu mniejsze niż ustawienie limitu czasu bezczynności lub zwiększ wartość limitu czasu bezczynności. Aby zapewnić obsługę takich scenariuszy, Dodaliśmy obsługę można skonfigurować limit czasu bezczynności. Można teraz ustawić czas od 4 do 30 minut.

TCP keep-alive działa dobrze w przypadku scenariuszy, w której czas pracy baterii nie jest ograniczeniem. Nie jest zalecane dla aplikacji mobilnych. Za pomocą protokołu TCP keep-alive w aplikacji mobilnej można szybciej wyczerpania baterii urządzenia.

![Limit czasu protokołu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Jak zmienić ustawienia limitu czasu bezczynności w przypadku maszyn wirtualnych i usług w chmurze można znaleźć w następujących sekcjach.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurowanie limitu czasu protokołu TCP dla usługi na poziomie wystąpienia publiczny adres IP na 15 minut

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

Parametr `IdleTimeoutInMinutes` jest opcjonalny. Jeśli nie jest ustawiona, domyślna wartość limitu czasu jest 4 minuty. Zakres dopuszczalny limit czasu wynosi 4 do 30 minut.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Ustaw limit czasu bezczynności, podczas tworzenia punktu końcowego platformy Azure na maszynie wirtualnej

Aby zmienić ustawienia limitu czasu dla punktu końcowego, użyj następującego polecenia:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Aby pobrać konfigurację limitu czasu bezczynności, użyj następującego polecenia:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Ustawianie limitu czasu protokołu TCP w zestawie końcowy z równoważeniem obciążenia

Jeśli punkty końcowe są częścią zestawu końcowy z równoważeniem obciążenia, w zestawie końcowy z równoważeniem obciążenia należy ustawić limitu czasu protokołu TCP. Na przykład:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Zmienić ustawienia limitu czasu dla usług w chmurze

Zestaw Azure SDK umożliwia aktualizowanie usługi w chmurze. Wprowadzone ustawienia punktu końcowego usług w chmurze w pliku csdef. Aktualizowanie limitu czasu protokołu TCP dla wdrożenia usługi w chmurze wymaga uaktualnienia wdrożenia. Wyjątkiem jest, jeśli określono limitu czasu protokołu TCP, tylko w przypadku publicznego adresu IP. Ustawienia publicznego adresu IP znajdują się w pliku .cscfg. Ponadto można aktualizować za pośrednictwem aktualizacji wdrażania i uaktualniania.

Zmiany .csdef ustawień punktu końcowego są następujące:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Zmiany .cscfg ustawienia limitu czasu na publiczne adresy IP są następujące:

```xml
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

## <a name="rest-api-example"></a>Przykład interfejsu API REST

Limit czasu bezczynności protokołu TCP można skonfigurować za pomocą interfejsu API zarządzania usługami. Upewnij się, że `x-ms-version` nagłówka ustawiono wersję `2014-06-01` lub nowszej. Zaktualizuj konfigurację określonego ze zrównoważonym obciążeniem wejściowych punktów końcowych na wszystkich maszynach wirtualnych w ramach wdrożenia.

### <a name="request"></a>Żądanie

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Odpowiedź

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Kolejne kroki

[Omówienie modułu równoważenia obciążenia wewnętrznego](load-balancer-internal-overview.md)

[Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](load-balancer-get-started-internet-arm-ps.md)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)
