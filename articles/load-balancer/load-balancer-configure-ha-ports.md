---
title: Konfigurowanie portów wysokiej dostępności dla modułu równoważenia obciążenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak używać portów wysokiej dostępności dla wewnętrznego ruchu na wszystkich portach równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c2e787a1f81d9f3d31b981c31a0249dd362b7bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225402"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurowanie portów wysokiej dostępności dla wewnętrznego modułu równoważenia obciążenia

Ten artykuł zawiera przykładowe wdrożenie portów wysokiej dostępności na wewnętrznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji na temat konfiguracji specyficzne dla sieci wirtualnych urządzeń sieciowych (urządzeń WUS) zobacz odpowiedni witryn sieci Web dostawcy.

>[!NOTE]
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono standardowego modułu równoważenia obciążenia. Aby uzyskać więcej informacji na temat podstawowego modułu równoważenia obciążenia, zobacz [omówienie Load Balancer](load-balancer-overview.md).

Na ilustracji przedstawiono następującą konfigurację przykład wdrożenia opisano w tym artykule:

- Urządzenia WUS są wdrażane w puli zaplecza wewnętrznego modułu równoważenia obciążenia wprowadzonych przez tę konfigurację portów wysokiej dostępności. 
- Trasy zdefiniowane przez użytkownika (przez) stosowane na trasach podsieci sieci obwodowej cały ruch do NVAs dokonując następnego przeskoku jako wewnętrznego wirtualnego adresu IP usługi równoważenia obciążenia. 
- Wewnętrzny moduł równoważenia obciążenia dystrybuuje ruch do jednego z aktywnego urządzenia WUS zgodnie z algorytmu równoważenia obciążenia.
- Urządzenie WUS przetwarza dane i przekazuje go do początkowego miejsca docelowego w podsieci zaplecza.
- Ścieżki zwrotu może potrwać tej samej trasie, jeśli odpowiednie trasy zdefiniowanej przez użytkownika jest konfigurowane w podsieci zaplecza. 

![Wdrażanie przykładu portów wysokiej dostępności](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurowanie portów wysokiej dostępności

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Konfigurowanie portów wysokiej dostępności, ułożyć wewnętrznego modułu równoważenia obciążenia przy użyciu urządzeń WUS w puli zaplecza. Konfigurowanie odpowiedniej konfiguracji sondy kondycji modułu równoważenia obciążenia, wykrywanie kondycji urządzenia WUS i reguły modułu równoważenia obciążenia przy użyciu portów wysokiej dostępności. Konfiguracji dotyczące modułu równoważenia obciążenia ogólne są omówione w [wprowadzenie](load-balancer-get-started-ilb-arm-portal.md). W tym artykule przedstawiono konfigurację portów wysokiej dostępności.

Konfiguracja wiąże się ustawienie portu frontonu i wartość portu zaplecza do **0**. Ustaw wartość protokołu **wszystkich**. W tym artykule opisano sposób konfigurowania portów wysokiej dostępności przy użyciu witryny Azure portal, programu PowerShell i wiersza polecenia platformy Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurowanie portów wysokiej dostępności reguły modułu równoważenia obciążenia za pomocą witryny Azure portal

Aby skonfigurować portów wysokiej dostępności przy użyciu witryny Azure portal, wybierz pozycję **porty wysokiej dostępności** pole wyboru. Po wybraniu związanej z nimi konfiguracji portu i protokołu jest wypełniane automatycznie. 

![Konfiguracja portów wysokiej dostępności za pośrednictwem witryny Azure portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurowanie portów wysokiej dostępności reguły równoważenia obciążenia za pomocą szablonu usługi Resource Manager

Za konfigurowanie portów wysokiej dostępności przy użyciu wersji interfejsu API 2017-08-01 dla Microsoft.Network/loadBalancers w zasobie usługi równoważenia obciążenia. Poniższy fragment kodu JSON przedstawia zmiany w konfiguracji usługi równoważenia obciążenia dla portów wysokiej dostępności za pośrednictwem interfejsu API REST:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Skonfiguruj reguły modułu równoważenia obciążenia portów wysokiej dostępności przy użyciu programu PowerShell

Aby utworzyć reguły modułu równoważenia obciążenia portów wysokiej dostępności, podczas tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu programu PowerShell, użyj następującego polecenia:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Skonfiguruj reguły modułu równoważenia obciążenia portów wysokiej dostępności przy użyciu wiersza polecenia platformy Azure

W kroku 4 [Tworzenie zestawu modułu równoważenia obciążenia wewnętrznego](load-balancer-get-started-ilb-arm-cli.md), użyj następującego polecenia, aby utworzyć regułę modułu równoważenia obciążenia portów wysokiej dostępności:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [portów wysokiej dostępności](load-balancer-ha-ports-overview.md).