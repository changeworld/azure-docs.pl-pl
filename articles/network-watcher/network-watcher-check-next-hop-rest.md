---
title: "Znajdź następny przeskok z sieci platformy Azure obserwatora następnego przeskoku - REST | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano, jak znaleźć, co to jest typ następnego przeskoku i adres ip za pomocą następnego przeskoku przy użyciu interfejsu API REST Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Sprawdź, jaki typ następnego przeskoku jest przy użyciu funkcji w następnym przeskoku w obserwatora sieciowego Azure przy użyciu interfejsu API REST Azure

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-check-next-hop-portal.md)
> - [Program PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-check-next-hop-cli.md)
> - [Interfejs API Azure REST](network-watcher-check-next-hop-rest.md)

Następny przeskok jest funkcją obserwatora sieciowego, który zapewnia możliwość get Typ następnego przeskoku i adres IP na podstawie określonej maszyny wirtualnej. Ta funkcja jest przydatna przy określaniu, jeśli ruchu wychodzącego maszyny wirtualnej są przesyłane za pośrednictwem bramy, Internetu lub sieci wirtualne na uzyskanie dostępu do miejsca docelowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

ARMclient służy do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule używa następnego przeskoku, funkcja obserwatora sieciowego, który znajduje się typ następnego przeskoku i adres IP dla każdego zasobu. Aby dowiedzieć się więcej na temat następnego przeskoku, odwiedź stronę [następnego przeskoku omówienie](network-watcher-next-hop-overview.md).

W tym scenariuszu obejmują:

* Pobrać następnego skoku dla maszyny wirtualnej.

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

Zaloguj się do armclient przy użyciu poświadczeń platformy Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Pobieranie maszyny wirtualnej

Uruchom następujący skrypt, aby wrócić do maszyny wirtualnej. Te informacje są potrzebne do uruchomienia następnego przeskoku.

Poniższy kod wymaga wartości dla następujących zmiennych:

- **Identyfikator subskrypcji** — identyfikator do użycia subskrypcji.
- **resourceGroupName** — Nazwa grupy zasobów, która zawiera maszyny wirtualne.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Z następujących danych wyjściowych identyfikator maszyny wirtualnej jest używany w następującym przykładzie:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Pobierz następnego skoku

Po utworzeniu nagłówek autoryzacji można pobrać następnego przeskoku z maszyny wirtualnej. Następujące wartości należy zastąpić, na przykład kodu do pracy.

> [!Important]
> Dla wywołań interfejsu API REST obserwatora sieciowego się, że nazwa grupy zasobów w identyfikatorze URI żądania jest grupę zasobów, która zawiera obserwatora sieciowego nie zasoby są wykonywane czynności diagnostyczne na.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Następnego przeskoku wymaga, aby zasobu maszyny Wirtualnej jest przydzielona do uruchomienia.

## <a name="results"></a>Wyniki

Poniższy fragment jest przykładem otrzymane dane wyjściowe. Wyniki zawierają następujące wartości:

* **Typ następnego przeskoku** — ta wartość jest jednym z następujących wartości: Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway lub None.
* **adres IP następnego przeskoku** -adres IP następnego przeskoku.
* **routeTableId** — wartość jest albo identyfikatorem uri dla tabeli tras skojarzone z trasą lub jeśli zdefiniowane przez użytkownika nie zdefiniowano trasy wartość *trasa systemowa* jest zwracany.

Poniżej przedstawiono wyniki w formacie json.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Kolejne kroki

Po mógł sprawdzić następnego skoku dla maszyny wirtualnej, można wyświetlić bezpieczeństwa zasobów sieciowych, odwiedzając [widok zabezpieczeń — omówienie](network-watcher-security-group-view-overview.md)














