---
title: Konfigurowanie wysokiej dostępności portów dla usługi równoważenia obciążenia Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie korzystania z wysokiej dostępności portów do wewnętrznego ruchu na wszystkich portach równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 117e73c35bb66578976ef990e61eea606e2e8e36
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736885"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurowanie wysokiej dostępności portów dla wewnętrznego modułu równoważenia obciążenia

W tym artykule przedstawiono przykładowe wdrożenie wysokiej dostępności porty na wewnętrzny moduł równoważenia obciążenia. Aby uzyskać więcej informacji na temat konfiguracji specyficznych dla sieci wirtualnych urządzeń (NVAs) Zobacz odpowiedniej witryny sieci Web dostawcy.

>[!NOTE]
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono standardowe usługi równoważenia obciążenia. Aby uzyskać więcej informacji na temat podstawowe usługi równoważenia obciążenia, zobacz [Omówienie usługi równoważenia obciążenia](load-balancer-overview.md).

Na ilustracji przedstawiono następującą konfigurację przykład wdrożenia, opisane w tym artykule:

- NVAs są wdrażane w puli zaplecza modułu równoważenia obciążenia wewnętrznego za konfiguracji wysokiej dostępności portów. 
- Trasy zdefiniowane przez użytkownika (przez) stosowane na trasach podsieci sieci obwodowej cały ruch do NVAs dokonując następnego przeskoku jako wewnętrznego wirtualnego adresu IP usługi równoważenia obciążenia. 
- Wewnętrzny moduł równoważenia obciążenia dystrybuuje ruch do jednego z active NVAs zgodnie z algorytmem usługi równoważenia obciążenia.
- Analizę NVA przetwarza ruchu i przekazuje je do początkowego miejsca docelowego w podsieci wewnętrznej.
- Ścieżka zwracanego może zająć tej samej ścieżki, jeśli odpowiednie przez jest skonfigurowany w podsieci wewnętrznej. 

![Wysokiej dostępności porty Przykładowe wdrożenie](./media/load-balancer-configure-ha-ports/haports.png)



## <a name="configure-high-availability-ports"></a>Konfigurowanie wysokiej dostępności portów

Aby skonfigurować wysokiej dostępności porty, skonfigurować wewnętrzny moduł równoważenia obciążenia z NVAs w puli zaplecza. Konfigurowanie odpowiedniej konfiguracji sondy kondycji modułu równoważenia obciążenia, wykrywanie kondycji NVA i reguły modułu równoważenia obciążenia o wysokiej dostępności portów. Omówiono konfiguracji odnoszące się do usługi równoważenia obciążenia ogólne [wprowadzenie](load-balancer-get-started-ilb-arm-portal.md). W tym artykule omówiono Konfigurowanie wysokiej dostępności portów.

Zasadniczo obejmuje konfiguracji ustawień portów frontonu i wartość portu zaplecza do **0**. Ustaw wartość protokołu **wszystkich**. W tym artykule opisano sposób konfigurowania wysokiej dostępności portów przy użyciu portalu Azure, programu PowerShell i Azure CLI 2.0.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurowanie wysokiej dostępności porty reguły modułu równoważenia obciążenia z portalu Azure

Aby skonfigurować wysokiej dostępności portów za pomocą portalu Azure, wybierz **HA porty** pole wyboru. Po wybraniu elementami konfiguracji portu i protokołu jest wypełniane automatycznie. 

![Konfigurowanie wysokiej dostępności portów za pośrednictwem portalu Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Skonfiguruj regułę równoważenia obciążenia wysokiej dostępności portów za pomocą szablonu usługi Resource Manager

Wysokiej dostępności portów można skonfigurować przy użyciu wersji interfejsu API 2017-08-01 dla Microsoft.Network/loadBalancers w zasobie usługi równoważenia obciążenia. Poniższy fragment kodu JSON przedstawia zmiany w konfiguracji usługi równoważenia obciążenia dla wysokiej dostępności portów za pomocą interfejsu API REST:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Skonfiguruj reguły modułu równoważenia obciążenia wysokiej dostępności portów przy użyciu programu PowerShell

Aby utworzyć regułę modułu równoważenia obciążenia wysokiej dostępności portów podczas tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu programu PowerShell, użyj następującego polecenia:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Konfigurowanie wysokiej dostępności porty reguły modułu równoważenia obciążenia Azure CLI 2.0

W kroku 4 [utworzenie zestawu modułu równoważenia obciążenia wewnętrznego](load-balancer-get-started-ilb-arm-cli.md), użyj następującego polecenia, aby utworzyć regułę modułu równoważenia obciążenia wysokiej dostępności porty:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [wysokiej dostępności porty](load-balancer-ha-ports-overview.md).
