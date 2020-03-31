---
title: Tworzenie reguły równoważenia obciążenia platformy Azure dla klastra
description: Skonfiguruj moduł równoważenia obciążenia platformy Azure, aby otwierać porty dla klastra sieci szkieletowej usług Azure.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: f4599b2e0174381ab7df04aeeb33db7e3ee60f26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025388"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Otwieranie portów dla klastra usługi Service Fabric

Moduł równoważenia obciążenia wdrożony za pomocą klastra sieci szkieletowej usług Azure kieruje ruch do aplikacji uruchomionej w węźle. Jeśli zmienisz aplikację, aby użyć innego portu, należy udostępnić tego portu (lub trasy innego portu) w azure load balancer.

Po wdrożeniu klastra sieci szkieletowej usług na platformie Azure moduł równoważenia obciążenia został automatycznie utworzony. Jeśli nie masz modułu równoważenia obciążenia, zobacz [Konfigurowanie modułu równoważenia obciążenia skierowanego do Internetu](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurowanie sieci szkieletowej usług

Aplikacja **servicemanifest.xml** aplikacji sieci szkieletowej definiuje punkty końcowe aplikacji oczekuje się używać. Po zaktualizowaniu pliku konfiguracji w celu zdefiniowania punktu końcowego moduł równoważenia obciążenia musi zostać zaktualizowany w celu udostępnienia tego (lub innego) portu. Aby uzyskać więcej informacji na temat tworzenia punktu końcowego sieci szkieletowej usług, zobacz [Konfigurowanie punktu końcowego](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia otwiera port internetowy i przekazuje ruch do portu węzła wewnętrznego używanego przez aplikację. Jeśli nie masz modułu równoważenia obciążenia, zobacz [Konfigurowanie modułu równoważenia obciążenia skierowanego do Internetu](../load-balancer/load-balancer-get-started-internet-portal.md).

Aby utworzyć regułę równoważenia obciążenia, należy zebrać następujące informacje:

- Nazwa modułu równoważenia obciążenia.
- Grupa zasobów modułu równoważenia obciążenia i klastra sieci szkieletowej usług.
- Port zewnętrzny.
- Port wewnętrzny.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Wystarczy jedno polecenie, aby utworzyć regułę równoważenia obciążenia za pomocą **interfejsu wiersza polecenia platformy Azure.** Wystarczy znać zarówno nazwę modułu równoważenia obciążenia, jak i grupę zasobów, aby utworzyć nową regułę.

>[!NOTE]
>Jeśli chcesz określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich modułów równoważenia obciążenia i skojarzonych grup zasobów.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Polecenie interfejsu wiersza polecenia platformy Azure ma kilka parametrów, które są opisane w poniższej tabeli:

| Parametr | Opis |
| --------- | ----------- |
| `--backend-port`  | Port, który nasłuchuje aplikacja sieci szkieletowej usług. |
| `--frontend-port` | Port, który moduł równoważenia obciążenia udostępnia dla połączeń zewnętrznych. |
| `-lb-name` | Nazwa modułu równoważenia obciążenia do zmiany. |
| `-g`       | Grupa zasobów, która ma zarówno moduł równoważenia obciążenia, jak i klaster sieci szkieletowej usług. |
| `-n`       | Żądana nazwa reguły. |


>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie modułu równoważenia obciążenia za pomocą interfejsu wiersza polecenia platformy Azure.](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)

## <a name="powershell"></a>PowerShell

Program PowerShell jest nieco bardziej skomplikowane niż interfejsu wiersza polecenia platformy Azure. Wykonaj następujące kroki koncepcyjne, aby utworzyć regułę:

1. Pobierz moduł równoważenia obciążenia z platformy Azure.
2. Utwórz regułę.
3. Dodaj regułę do modułu równoważenia obciążenia.
4. Zaktualizuj moduł równoważenia obciążenia.

>[!NOTE]
>Jeśli chcesz określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich modułów równoważenia obciążenia i skojarzonych grup zasobów.
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

W `New-AzLoadBalancerRuleConfig` odniesieniu do `-FrontendPort` polecenia reprezentuje port, który moduł równoważenia `-BackendPort` obciążenia udostępnia dla połączeń zewnętrznych, a reprezentuje port, który nasłuchuje aplikacja sieci szkieletowej usług.

>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia za pomocą programu PowerShell, zobacz [Tworzenie modułu równoważenia obciążenia za pomocą programu PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [sieci w sieci szkieletowej usług](service-fabric-patterns-networking.md).
