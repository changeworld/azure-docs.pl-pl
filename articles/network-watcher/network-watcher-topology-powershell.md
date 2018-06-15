---
title: Wyświetl topologii obserwatora sieciowego Azure - PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zapytania topologii sieci przy użyciu programu PowerShell.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864378"
---
# <a name="view-network-watcher-topology-with-powershell"></a>Wyświetl topologii obserwatora sieciowego przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-topology-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-topology-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-topology-cli.md)
> - [Interfejs API REST](network-watcher-topology-rest.md)

Funkcja topologii sieci obserwatora zapewnia wizualną reprezentację zasobów sieciowych w ramach subskrypcji. W portalu tej wizualizacji są prezentowane automatycznie. Informacje dotyczące widoku topologii w portalu można pobrać za pomocą programu PowerShell.
Dzięki temu informacje topologii bardziej elastyczne, dane mogą być używane przez inne narzędzia do tworzenia wizualizacji.

W obszarze dwie relacje w modelu połączenia.

- **Zawieranie** — przykład: sieć wirtualna zawiera podsieć zawiera karty Sieciowej
- **Skojarzone** — przykład: karta sieciowa jest skojarzona z maszyną Wirtualną

Poniżej znajduje się właściwości, które są zwracane podczas wykonywania zapytania interfejsu API REST topologii.

* **Nazwa** — Nazwa zasobu
* **Identyfikator** — identyfikator uri zasobu.
* **Lokalizacja** — lokalizacji, w której istnieje zasób.
* **skojarzenia** -listę skojarzeń odwołuje się do obiektu.
    * **Nazwa** — Nazwa zasobu, do którego istnieje odwołanie.
    * **resourceId** — element resourceId jest identyfikatorem uri zasobu, do którego odwołuje się powiązanie.
    * **Obiekt associationType** — ta wartość odwołuje się do relacji między obiekt podrzędny i obiektu nadrzędnego. Prawidłowe wartości to **zawiera** lub **skojarzone**.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu, należy użyć `Get-AzureRmNetworkWatcherTopology` polecenia cmdlet można pobrać informacji o topologii. Istnieje również artykuł na temat [pobrać topologii sieci z interfejsu API REST](network-watcher-topology-rest.md).

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule pobiera odpowiedź topologii dla określonej grupy zasobów.

## <a name="retrieve-network-watcher"></a>Pobrać obserwatora sieciowego

Pierwszym krokiem jest pobrać wystąpienia obserwatora sieciowego. `$networkWatcher` Zmienna jest przekazywana do `Get-AzureRmNetworkWatcherTopology` polecenia cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Pobrać topologii

`Get-AzureRmNetworkWatcherTopology` Polecenie cmdlet pobiera topologii dla określonej grupy zasobów.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Wyniki

Wyniki zwrócone ma właściwości name "zasoby", które zawiera treści odpowiedzi json dla `Get-AzureRmNetworkWatcherTopology` polecenia cmdlet.  Odpowiedź zawiera zasoby w grupie zabezpieczeń sieci i ich powiązania (to znaczy zawiera, skojarzone).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wizualizacji NSG dzienników przepływu z usługi Power BI, odwiedzając [wizualizacji NSG przepływa dzienników przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


