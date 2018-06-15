---
title: Wyświetl topologii obserwatora sieciowego Azure - interfejsu API REST | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia interfejsu API REST do badania topologii sieci.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864182"
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Wyświetl topologią obserwatora sieciowego za pomocą interfejsu API REST

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

W tym scenariuszu można pobrać informacji o topologii. ARMclient służy do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule pobiera odpowiedź topologii dla określonej grupy zasobów.

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

Zaloguj się do armclient przy użyciu poświadczeń platformy Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Pobrać topologii

Poniższy przykład żądań topologii z interfejsu API REST.  Przykład jest parametry umożliwiające elastyczność tworzenia przykładem.  Zamień wszystkie wartości z \< \> między nimi.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

Następującą odpowiedź jest przykładem skróconą odpowiedzi, który jest zwracany, gdy pobrać topologii grupa zasobów:

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wizualizacji NSG dzienników przepływu z usługi Power BI, odwiedzając [wizualizacji NSG przepływa dzienników przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

