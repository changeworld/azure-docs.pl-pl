---
title: Tworzenie wewnętrznego modułu równoważenia obciążenia dla usług Azure Cloud Services — wdrożenie klasyczne
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć wewnętrzny moduł równoważenia obciążenia w klasycznym modelu wdrażania przy użyciu programu PowerShell
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 361322ded68f7c8305c4f976847d4bcb82f7e595
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626166"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Wprowadzenie do tworzenia wewnętrznego modułu równoważenia obciążenia (klasycznego) dla usług w chmurze

> [!div class="op_single_selector"]
> * [Program PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Usługi w chmurze](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak [wykonać te kroki przy użyciu modelu usługi Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Konfigurowanie wewnętrznego modułu równoważenia obciążenia dla usług w chmurze

Wewnętrzny moduł równoważenia obciążenia może być zastosowany zarówno do maszyn wirtualnych, jak i usług w chmurze. Punkt końcowy wewnętrznego modułu równoważenia obciążenia utworzony w usłudze w chmurze, poza regionalną siecią wirtualną, będzie dostępny tylko w ramach usługi w chmurze.

Konfigurację wewnętrznego modułu równoważenia obciążenia należy ustawić podczas tworzenia pierwszego wdrożenia w usłudze w chmurze, jak pokazano w przykładzie poniżej.

> [!IMPORTANT]
> Wstępnym wymogiem do wykonania poniższych kroków jest posiadanie utworzonej sieci wirtualnej w celu wdrożenia chmury. Do utworzenia wewnętrznego równoważenia obciążenia potrzebna będzie nazwa sieci wirtualnej i podsieci.

### <a name="step-1"></a>Krok 1

Otwórz plik konfiguracji usługi (.cscfg) dla danego wdrożenia chmury w programie Visual Studio i dodaj następującą sekcję, aby utworzyć wewnętrzne równoważenie obciążenia w ramach ostatniego elementu „`</Role>`” konfiguracji sieciowej.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Na tym etapie dodawane są wartości pliku konfiguracji sieci, aby pokazać efekt końcowy. W przykładzie przyjęto założenie, że utworzono sieć wirtualną o nazwie „test_vnet” z podsiecią 10.0.0.0/24 o nazwie „test_subnet” i statycznym adresie IP 10.0.0.4. Moduł równoważenia obciążenia będzie miał nazwę „testLB”.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Aby uzyskać więcej informacji o schemacie modułu równoważenia obciążenia, zobacz [Add load balancer](https://msdn.microsoft.com/library/azure/dn722411.aspx) (Dodawanie modułu równoważenia obciążenia).

### <a name="step-2"></a>Krok 2

Zmień plik definicji usługi (.csdef) tak, aby dodać punkty końcowe do wewnętrznego równoważenia obciążenia. W momencie utworzenia wystąpienia roli plik definicji usługi spowoduje dodanie wystąpień roli do wewnętrznego równoważenia obciążenia.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

Wykorzystując wartości użyte w powyższym przykładzie, dodaj wartości do pliku definicji usługi.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

Obciążenie ruchu sieciowego zostanie zrównoważone przy użyciu modułu równoważenia obciążenia „testLB” za pomocą portu 80 dla żądań przychodzących przez przesłanie do wystąpień roli procesu roboczego również na port 80.

## <a name="next-steps"></a>Kolejne kroki

[Configure a load balancer distribution mode using source IP affinity](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia przy użyciu koligacji źródłowych adresów IP)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)

