---
title: Konfigurowanie usługi równoważenia obciążenia dla programu SQL Server zawsze na | Dokumentacja firmy Microsoft
description: Konfigurowanie równoważenia obciążenia, aby pracować z programu SQL Server zawsze włączone i Dowiedz się, jak utworzyć usługę równoważenia obciążenia dla wdrożenia programu SQL Server przy użyciu programu PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: a0c2345b47b9103ac6a7ae998f13a12332e3907e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
ms.locfileid: "30262521"
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Konfigurowanie równoważenia obciążenia dla programu SQL Server AlwaysOn



SQL Server zawsze włączonych grup dostępności można teraz uruchomić z wewnętrznego modułu równoważenia obciążenia. Grupa dostępności to SQL Server najważniejszych rozwiązania wysokiej dostępności i odzyskiwania po awarii. Odbiornik grupy dostępności pozwala klienta aplikacjom bezproblemowo połączyć się repliką podstawową, niezależnie od liczby replik w konfiguracji.

Nazwa odbiornika (DNS) jest mapowany na adres IP usługi równoważenia obciążenia. Moduł równoważenia obciążenia Azure kieruje ruch przychodzący do serwera podstawowego zestawu replik.

Obsługa usługi równoważenia obciążenia wewnętrznego można użyć dla punktów końcowych SQL Server zawsze włączone (odbiornika). Masz teraz kontrolę nad dostępność odbiornika. Możesz adresu IP usługi równoważenia obciążenia z określonej podsieci w sieci wirtualnej.

Za pomocą wewnętrznego modułu równoważenia na obiektu nasłuchującego punktu końcowego programu SQL Server obciążenia (na przykład serwer = tcp:ListenerName, 1433; baza danych = DatabaseName) jest dostępny tylko dla:

* Usługi i maszyn wirtualnych w tej samej sieci wirtualnej.
* Usługi i maszyn wirtualnych z sieci połączonych lokalnie.
* Usługi i maszyn wirtualnych z połączonych sieciach wirtualnych.

![Wewnętrzny moduł równoważenia obciążenia programu SQL Server AlwaysOn](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Dodawanie wewnętrznego modułu równoważenia obciążenia z usługą

1. W poniższym przykładzie możesz skonfigurować siecią wirtualną, która zawiera podsieć o nazwie "Podsieć 1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Dodaj równoważeniem obciążenia punktów końcowych dla wewnętrznego modułu równoważenia obciążenia na każdej maszynie Wirtualnej.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    W poprzednim przykładzie masz dwie maszyn wirtualnych o nazwie "sqlsvc1" i "sqlsvc2" w usłudze w chmurze "Sqlsvc". Po utworzeniu wewnętrznego modułu równoważenia obciążenia z `DirectServerReturn` przełącznika, dodawanie punktów końcowych z równoważeniem obciążenia do wewnętrznego modułu równoważenia obciążenia. Zezwalaj na SQL Server, aby skonfigurować odbiorników dla grupy dostępności punktów końcowych z równoważeniem obciążenia.

Aby uzyskać więcej informacji na temat programu SQL Server AlwaysOn zobacz [skonfigurować wewnętrzny moduł równoważenia obciążenia dla grupy dostępności AlwaysOn w usłudze Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Zobacz także
* [Przed rozpoczęciem konfigurowania publiczny moduł równoważenia obciążenia](load-balancer-get-started-internet-arm-ps.md)
* [Get started configuring an internal load balancer](load-balancer-get-started-ilb-arm-ps.md) (Wprowadzenie do konfigurowania wewnętrznego modułu równoważenia obciążenia)
* [Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
