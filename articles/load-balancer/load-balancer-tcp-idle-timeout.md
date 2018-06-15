---
title: Skonfiguruj limit czasu bezczynności TCP usługi równoważenia obciążenia | Dokumentacja firmy Microsoft
description: Skonfiguruj limit czasu bezczynności TCP usługi równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 4625c6a8-5725-47ce-81db-4fa3bd055891
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: f19ac77f7c7f7d4ab8909d628f9dcce08c07c928
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855229"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Skonfiguruj ustawienia limitu czasu bezczynności TCP dla usługi równoważenia obciążenia Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

W konfiguracji domyślnej usługi równoważenia obciążenia Azure ma ustawienie limitu czasu bezczynności 4 minut. Jeśli okres aktywności jest dłuższy niż wartość limitu czasu, nie ma żadnej gwarancji, sesji TCP lub HTTP jest zachowywane między klientem i usługi w chmurze.

Gdy połączenie jest zamknięte, aplikacja kliencka może zostać wyświetlony następujący komunikat o błędzie: "Połączenie podstawowe zostało zakończone: połączenie, które miało być aktywne zostało zamknięte przez serwer."

Popularną praktyką jest Użyj TCP keep-alive. Takie rozwiązanie pozostawia połączenie jako aktywne przez dłuższy czas. Aby uzyskać więcej informacji, zobacz te [przykłady .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Włączone keep-alive, pakiety są wysyłane podczas nieaktywności dla połączenia. Te pakiety utrzymywania aktywności Sprawdź, czy wartość limitu czasu bezczynności został nigdy osiągnięty i połączenie jest obsługiwany przez dłuższy okres.

To ustawienie działa tylko w przypadku połączeń przychodzących. Aby uniknąć utraty połączenia, możesz skonfigurować interwał mniejsza niż wartość ustawienia limit czasu bezczynności keep-alive TCP lub zwiększ wartość limitu czasu bezczynności. Do obsługi tych scenariuszy, dodano obsługę można skonfigurować limit czasu bezczynności. Można teraz ustawić czas od 4 do 30 minut.

TCP keep-alive działa dobrze w przypadku scenariuszy, w których czas pracy baterii nie jest ograniczenie. Nie jest zalecane dla aplikacji dla urządzeń przenośnych. Przy użyciu TCP keep-alive w aplikacji mobilnej można szybciej opróżnienia baterii urządzenia.

![Limit czasu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

W poniższych sekcjach opisano sposób zmienić ustawienia limitu czasu bezczynności w przypadku maszyn wirtualnych i usług w chmurze.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurowanie limitu czasu protokołu TCP dla Twojego poziomie wystąpienia publicznego adresu IP na 15 minut

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

Parametr `IdleTimeoutInMinutes` jest opcjonalny. Jeśli nie jest ustawiona, domyślny limit czasu jest 4 minut. Zakres dopuszczalny limit czasu wynosi 4 do 30 minut.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Ustaw limit czasu bezczynności podczas tworzenia punktu końcowego platformy Azure na maszynie wirtualnej

Aby zmienić ustawienia limitu czasu dla punktu końcowego, użyj następującego polecenia:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Aby uzyskać konfigurację limit czasu bezczynności, użyj następującego polecenia:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Ustawianie limitu czasu TCP na zestaw z równoważeniem obciążenia punktu końcowego

Jeśli punkty końcowe są częścią zestawu o zrównoważonym obciążeniu punktu końcowego, należy ustawić limitu czasu TCP zestaw z równoważeniem obciążenia punktu końcowego. Na przykład:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Zmień ustawienia limitu czasu dla usług w chmurze

Zestaw SDK usługi Azure można użyć do aktualizacji usługi w chmurze. Wprowadzone ustawienia punktu końcowego usługi w chmurze w pliku csdef. Aktualizowanie limitu czasu protokołu TCP dla wdrożenia usługi w chmurze wymaga uaktualnienia wdrożenia. Wyjątek stanowi, jeśli limit czasu protokołu TCP jest określony tylko dla publicznego adresu IP. Ustawienia publicznego adresu IP znajdują się w pliku .cscfg i zaktualizować je za pośrednictwem aktualizacji wdrożenia i uaktualnienia.

Zmiany csdef ustawień punktu końcowego są następujące:

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

Limit czasu bezczynności TCP można skonfigurować przy użyciu interfejsu API zarządzania usługami. Upewnij się, że `x-ms-version` ustawiono nagłówka do wersji `2014-06-01` lub nowszej. Zaktualizuj konfigurację określonego równoważeniem obciążenia wejściowych punktów końcowych na wszystkich maszynach wirtualnych we wdrożeniu.

### <a name="request"></a>Żądanie

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Odpowiedź

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi równoważenia obciążenia wewnętrznego](load-balancer-internal-overview.md)

[Przed rozpoczęciem konfigurowania usługi równoważenia obciążenia połączonych z Internetem](load-balancer-get-started-internet-arm-ps.md)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)
