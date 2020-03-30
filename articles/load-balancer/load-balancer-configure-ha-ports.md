---
title: Konfigurowanie portów wysokiej dostępności dla modułu równoważenia obciążenia platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak używać portów o wysokiej dostępności do równoważenia obciążenia ruchu wewnętrznego na wszystkich portach
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
ms.openlocfilehash: c6b8ecb443408f23ae604bd9c8139cb0a2afcd12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477786"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurowanie portów wysokiej dostępności dla wewnętrznego modułu równoważenia obciążenia

W tym artykule przedstawiono przykładowe wdrożenie portów o wysokiej dostępności na wewnętrznym modułze równoważenia obciążenia. Aby uzyskać więcej informacji na temat konfiguracji specyficznych dla sieciowych urządzeń wirtualnych (NVA), zobacz odpowiednie witryny sieci Web dostawcy.

>[!NOTE]
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono standardowy moduł równoważenia obciążenia. Aby uzyskać więcej informacji na temat podstawowego modułu równoważenia obciążenia, zobacz [Omówienie modułu równoważenia obciążenia](load-balancer-overview.md).

Na ilustracji przedstawiono następującą konfigurację przykładu wdrożenia opisanego w tym artykule:

- Elementy WEWNĘTRZNEGO są wdrażane w puli zaplecza wewnętrznego modułu równoważenia obciążenia za konfiguracją portów o wysokiej dostępności. 
- Trasa zdefiniowana przez użytkownika (UDR) zastosowana w podsieci DMZ kieruje cały ruch do obiektów WDO, tworząc następny przeskok jako wirtualny adres IP modułu równoważenia obciążenia. 
- Wewnętrzny moduł równoważenia obciążenia rozprowadza ruch do jednego z aktywnych wydzieliń NVA zgodnie z algorytmem równoważenia obciążenia.
- Urządzenie WUS przetwarza ruch i przekazuje go do pierwotnego miejsca docelowego w podsieci zaplecza.
- Ścieżka powrotu może przyjąć tę samą trasę, jeśli odpowiednia instrukcja UDR jest skonfigurowana w podsieci zaplecza. 

![Przykładowe wdrożenie portów o wysokiej dostępności](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurowanie portów wysokiej dostępności

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby skonfigurować porty o wysokiej dostępności, należy skonfigurować wewnętrzny moduł równoważenia obciążenia z elementami wewnętrznego systemu WEWNĘTRZNEGO w puli zaplecza. Skonfiguruj odpowiednią konfigurację sondy kondycji modułu równoważenia obciążenia w celu wykrywania kondycji urządzenia WUS i reguły modułu równoważenia obciążenia za pomocą portów o wysokiej dostępności. Ogólna konfiguracja związana z modułem równoważenia obciążenia jest omówiona w [programie Wprowadzenie](load-balancer-get-started-ilb-arm-portal.md). W tym artykule przedstawiono konfigurację portów o wysokiej dostępności.

Konfiguracja polega zasadniczo na ustawieniu portu front-end i wartości portu zaplecza na **0**. Ustaw wartość protokołu na **Wszystkie**. W tym artykule opisano sposób konfigurowania portów o wysokiej dostępności przy użyciu witryny Azure portal, powershell i interfejsu wiersza polecenia platformy Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurowanie reguły modułu równoważenia obciążenia portów o wysokiej dostępności za pomocą witryny Azure portal

Aby skonfigurować porty o wysokiej dostępności przy użyciu portalu Azure portal, zaznacz pole wyboru **Porty wysokiej dostępności.** Po wybraniu tej opcji powiązany port i konfiguracja protokołu są automatycznie wypełniane. 

![Konfiguracja portów o wysokiej dostępności za pośrednictwem witryny Azure portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurowanie reguły równoważenia obciążenia portów o wysokiej dostępności za pomocą szablonu Menedżera zasobów

Porty o wysokiej dostępności można skonfigurować przy użyciu wersji interfejsu API 2017-08-01 dla programów Microsoft.Network/loadBalancers w zasobie modułu równoważenia obciążenia. Poniższy fragment kodu JSON ilustruje zmiany w konfiguracji modułu równoważenia obciążenia dla portów o wysokiej dostępności za pośrednictwem interfejsu API REST:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurowanie reguły modułu równoważenia obciążenia portów o wysokiej dostępności za pomocą programu PowerShell

Użyj następującego polecenia, aby utworzyć regułę modułu równoważenia obciążenia portów wysokiej dostępności podczas tworzenia wewnętrznego modułu równoważenia obciążenia za pomocą programu PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurowanie reguły modułu równoważenia obciążenia portów o wysokiej dostępności za pomocą interfejsu wiersza polecenia platformy Azure

W kroku 4 [tworzenia wewnętrznego zestawu modułu równoważenia obciążenia](load-balancer-get-started-ilb-arm-cli.md)użyj następującego polecenia, aby utworzyć regułę modułu równoważenia obciążenia portów o wysokiej dostępności:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [portach o wysokiej dostępności](load-balancer-ha-ports-overview.md).