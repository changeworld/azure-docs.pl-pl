---
title: Konfigurowanie Load Balancer limitu czasu bezczynności protokołu TCP na platformie Azure
titlesuffix: Azure Load Balancer
description: Skonfiguruj limit czasu bezczynności protokołu TCP Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: b3df1ead7a3164ffd9a4b4acf8820d0f5b82cee3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274178"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Skonfiguruj ustawienia limitu czasu bezczynności protokołu TCP dla Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

W konfiguracji domyślnej Azure Load Balancer ma ustawienie limitu czasu bezczynności wynoszące 4 minuty. Jeśli okres braku aktywności jest dłuższy niż wartość limitu czasu, nie ma gwarancji, że sesja TCP lub HTTP jest utrzymywana między klientem a usługą w chmurze.

Gdy połączenie zostanie zamknięte, aplikacja kliencka może otrzymać następujący komunikat o błędzie: "Połączenie podstawowe zostało zamknięte: Połączenie, które miało być utrzymywane w stanie aktywności, zostało zamknięte przez serwer ".

Typowym zastosowaniem jest utrzymywanie aktywności protokołu TCP. Ta metoda utrzymuje, że połączenie jest aktywne przez dłuższy czas. Aby uzyskać więcej informacji, zobacz te [przykłady dla platformy .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Gdy włączona jest funkcja Keep-Alive, pakiety są wysyłane w trakcie okresów braku aktywności w ramach połączenia. Te pakiety Keep-Alive zapewniają, że wartość limitu czasu bezczynności nigdy nie zostanie osiągnięta, a połączenie jest utrzymywane przez długi czas.

To ustawienie działa tylko dla połączeń przychodzących. Aby uniknąć utraty połączenia, należy skonfigurować opcję utrzymywania aktywności TCP z interwałem mniejszym niż limit czasu bezczynności lub zwiększyć wartość limitu czasu bezczynności. Aby obsługiwać takie scenariusze, dodaliśmy obsługę dla konfigurowalnego limitu czasu bezczynności. Teraz można ustawić ją na czas trwania od 4 do 30 minut.

Utrzymywanie aktywności TCP działa dobrze w przypadku scenariuszy, w których cykl życia baterii nie jest ograniczeniem. Nie jest to zalecane w przypadku aplikacji mobilnych. Korzystanie z funkcji utrzymywania połączenia TCP w aplikacji mobilnej umożliwia szybsze opróżnianie baterii urządzenia.

![Limit czasu TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

W poniższych sekcjach opisano, jak zmienić ustawienia limitu czasu bezczynności w maszynach wirtualnych i usługach w chmurze.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Skonfiguruj limit czasu protokołu TCP dla publicznego adresu IP na poziomie wystąpienia na 15 minut

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

Parametr `IdleTimeoutInMinutes` jest opcjonalny. Jeśli nie jest ustawiona, domyślny limit czasu wynosi 4 minuty. Akceptowalny zakres limitu czasu wynosi od 4 do 30 minut.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Ustaw limit czasu bezczynności podczas tworzenia punktu końcowego platformy Azure na maszynie wirtualnej

Aby zmienić ustawienie limitu czasu dla punktu końcowego, należy użyć następujących opcji:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Aby pobrać nieczynną konfigurację limitu czasu, użyj następującego polecenia:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Ustawianie limitu czasu protokołu TCP dla zestawu punktów końcowych ze zrównoważonym obciążeniem

Jeśli punkty końcowe są częścią zestawu punktów końcowych ze zrównoważonym obciążeniem, należy ustawić limit czasu protokołu TCP dla zestawu punktów końcowych ze zrównoważonym obciążeniem. Na przykład:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Zmień ustawienia limitu czasu dla usług Cloud Services

Możesz użyć zestawu Azure SDK, aby zaktualizować usługę w chmurze. Ustawienia punktu końcowego dla usług w chmurze są wprowadzane w pliku csdef. Aktualizacja limitu czasu TCP na potrzeby wdrożenia usługi w chmurze wymaga uaktualnienia wdrożenia. Wyjątek polega na tym, że limit czasu TCP jest określony tylko dla publicznego adresu IP. Ustawienia publicznego adresu IP znajdują się w pliku. cscfg i można je zaktualizować za pomocą aktualizacji i uaktualnienia wdrożenia.

Csdef zmiany ustawień punktu końcowego są następujące:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Zmiany w pliku cscfg dla ustawienia limitu czasu dla publicznych adresów IP to:

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

Limit czasu bezczynności protokołu TCP można skonfigurować przy użyciu interfejsu API zarządzania usługami. Upewnij się, że `x-ms-version` nagłówek jest ustawiony na wersję `2014-06-01` lub nowszą. Zaktualizuj konfigurację określonych punktów końcowych ze zrównoważonym obciążeniem na wszystkich maszynach wirtualnych we wdrożeniu.

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

[Przegląd wewnętrznego modułu równoważenia obciążenia](load-balancer-internal-overview.md)

[Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](load-balancer-get-started-internet-arm-ps.md)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)
