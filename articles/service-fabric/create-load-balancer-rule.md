---
title: Tworzenie reguły modułu równoważenia obciążenia Azure dla klastra
description: Konfigurowanie usługi równoważenia obciążenia Azure otworzyć porty dla klastra usługi sieć szkieletowa usług Azure.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 53dcd6c0705faa94e83d6e44f813fa9c575843e8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Otwieranie portów dla klastra sieci szkieletowej usług

Moduł równoważenia obciążenia z klastra usługi sieć szkieletowa usług Azure kieruje ruch do aplikacji uruchomionej w węźle. Jeśli zmienisz aplikację do używania innego portu, musi ujawniać tego portu (lub trasy innego portu) w usłudze równoważenia obciążenia Azure.

Po wdrożeniu klastra usługi sieć szkieletowa usług Azure modułu równoważenia obciążenia został utworzony automatycznie. Jeśli nie masz usługi równoważenia obciążenia, zobacz [skonfigurowania funkcji równoważenia obciążenia internetowy](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>Konfigurowanie sieci szkieletowej usług

Aplikacja usługi Service Fabric **ServiceManifest.xml** pliku konfiguracyjnego definiuje punkty końcowe aplikacji zamierza użyć. Po plik konfiguracji został zaktualizowany tak, aby zdefiniować punkt końcowy, Usługa równoważenia obciążenia muszą zostać zaktualizowane do udostępnienia, który (lub innej) portu. Aby uzyskać więcej informacji na temat tworzenia punktu końcowego sieci szkieletowej usług, zobacz [Konfiguracja punktu końcowego](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia otwiera port połączonych z Internetem i przekazuje ruch do portu węzeł wewnętrzny używany przez aplikację. Jeśli nie masz usługi równoważenia obciążenia, zobacz [skonfigurowania funkcji równoważenia obciążenia internetowy](..\load-balancer\load-balancer-get-started-internet-portal.md).

Aby utworzyć regułę modułu równoważenia obciążenia, które należy zebrać następujące informacje:

- Nazwa modułu równoważenia obciążenia.
- Grupa zasobów klastra sieci szkieletowej usług i usługi równoważenia obciążenia.
- Portu zewnętrznego.
- Port wewnętrzny.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Wymaga tylko jednego polecenia, aby utworzyć regułę modułu równoważenia obciążenia z **interfejsu wiersza polecenia Azure**. Wystarczy znać nazwę grupy równoważenia i zasobów obciążenia, aby utworzyć nową regułę.

>[!NOTE]
>Jeśli trzeba określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich usług równoważenia obciążenia i grupy zasobów.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Polecenie wiersza polecenia platformy Azure ma kilka parametrów, które zostały opisane w poniższej tabeli:

| Parametr | Opis |
| --------- | ----------- |
| `--backend-port`  | Nasłuchuje portu aplikacji sieci szkieletowej usług. |
| `--frontend-port` | Udostępnia portu usługi równoważenia obciążenia dla połączeń zewnętrznych. |
| `-lb-name` | Nazwa modułu równoważenia obciążenia, aby zmienić. |
| `-g`       | Grupy zasobów, usługi równoważenia obciążenia i klaster sieci szkieletowej usług. |
| `-n`       | Żądana nazwa reguły. |


>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia z wiersza polecenia platformy Azure, zobacz [Utwórz usługę równoważenia obciążenia z wiersza polecenia platformy Azure](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell jest nieco bardziej skomplikowane niż wiersza polecenia platformy Azure. Wykonaj następujące kroki pojęciach, aby utworzyć regułę:

1. Pobierz moduł równoważenia obciążenia z platformy Azure.
2. Utwórz regułę.
3. Dodaj regułę modułu równoważenia obciążenia.
4. Aktualizacja usługi równoważenia obciążenia.

>[!NOTE]
>Jeśli trzeba określić nazwę modułu równoważenia obciążenia, użyj tego polecenia, aby szybko uzyskać listę wszystkich usług równoważenia obciążenia i grup zasobów.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

Dotyczące `New-AzureRmLoadBalancerRuleConfig` polecenia `-FrontendPort` reprezentuje port udostępnia usługi równoważenia obciążenia dla połączeń zewnętrznych i `-BackendPort` reprezentuje port nasłuchuje aplikacji sieci szkieletowej usług.

>[!NOTE]
>Aby uzyskać więcej informacji na temat tworzenia modułu równoważenia obciążenia przy użyciu programu PowerShell, zobacz [tworzenia modułu równoważenia obciążenia przy użyciu programu PowerShell](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [sieć w sieci szkieletowej usług](service-fabric-patterns-networking.md).