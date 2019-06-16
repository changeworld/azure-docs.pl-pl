---
title: Utwórz regułę modułu równoważenia obciążenia platformy Azure dla klastra
description: Konfigurowanie usługi Azure Load Balancer, aby otworzyć porty dla klastra usługi Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: aljo
ms.openlocfilehash: d95d2802398a61b948ff6c59fb3eab0e1ddddbc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147463"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Otwieranie portów dla klastra usługi Service Fabric

Wdrożenia z klastra usługi Azure Service Fabric modułu równoważenia obciążenia kieruje ruch do aplikacji uruchomionej w węźle. Możesz zmienić aplikację, aby użyć innego portu, należy udostępnić ten port (lub kierować inny port) w module równoważenia obciążenia platformy Azure.

Po wdrożeniu klastra usługi Service Fabric na platformie Azure równoważenia obciążenia został utworzony automatycznie. Jeśli nie masz usługi równoważenia obciążenia, zobacz [skonfigurować moduł równoważenia obciążenia dostępnego z Internetu](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurowanie usługi Service fabric

Aplikacja usługi Service Fabric **ServiceManifest.xml** pliku konfiguracyjnego definiuje punkty końcowe, aplikacja oczekuje, że do użycia. Po zaktualizowaniu pliku konfiguracji do definiowania punktu końcowego modułu równoważenia obciążenia należy zaktualizować tak, aby ujawnić, że (lub inną) port. Aby uzyskać więcej informacji na temat tworzenia punktu końcowego usługi Service fabric, zobacz [konfiguracji punktu końcowego](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia otwiera port połączone z Internetem i przekazuje ruch na porcie węzła wewnętrznych używanych przez aplikację. Jeśli nie masz usługi równoważenia obciążenia, zobacz [skonfigurować moduł równoważenia obciążenia dostępnego z Internetu](../load-balancer/load-balancer-get-started-internet-portal.md).

Aby utworzyć regułę modułu równoważenia obciążenia, należy zebrać następujące informacje:

- Nazwa modułu równoważenia obciążenia.
- Grupa zasobów klastra obciążenia równoważenia i usługi Service fabric.
- Porcie zewnętrznym.
- Wewnętrzny port.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Rejestracja trwa tylko jednego polecenia, aby utworzyć regułę modułu równoważenia obciążenia za pomocą **wiersza polecenia platformy Azure**. Po prostu musisz znać nazwę obciążenia równoważenia i grupę zasobów, aby utworzyć nową regułę.

>[!NOTE]
>Jeśli musisz określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich modułów równoważenia obciążenia i grupy zasobów.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Polecenie wiersza polecenia platformy Azure ma kilka parametrów, które są opisane w poniższej tabeli:

| Parametr | Opis |
| --------- | ----------- |
| `--backend-port`  | Port aplikacji usługi Service Fabric nasłuchuje. |
| `--frontend-port` | Uwidacznia port modułu równoważenia obciążenia dla połączeń zewnętrznych. |
| `-lb-name` | Nazwa modułu równoważenia obciążenia, aby zmienić. |
| `-g`       | Grupa zasobów, moduł równoważenia obciążenia i klaster usługi Service Fabric. |
| `-n`       | Nazwa żądanej reguły. |


>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure, zobacz [Tworzenie modułu równoważenia obciążenia przy użyciu wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

Program PowerShell jest nieco bardziej skomplikowane niż wiersza polecenia platformy Azure. Wykonaj następujące ogólne kroki, aby utworzyć regułę:

1. Pobierz moduł równoważenia obciążenia na platformie Azure.
2. Utwórz regułę.
3. Dodaj regułę równoważenia obciążenia.
4. Zaktualizuj moduł równoważenia obciążenia.

>[!NOTE]
>Jeśli musisz określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich modułów równoważenia obciążenia i grup zasobów skojarzonych.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

Dotyczące `New-AzLoadBalancerRuleConfig` polecenia `-FrontendPort` reprezentuje port modułu równoważenia obciążenia uwidacznia dla połączeń zewnętrznych i `-BackendPort` reprezentuje port nasłuchuje aplikacji usługi Service fabric.

>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia przy użyciu programu PowerShell, zobacz [Utwórz moduł równoważenia obciążenia przy użyciu programu PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [sieci w usłudze Service Fabric](service-fabric-patterns-networking.md).rvice-fabric wzorców networking.md).