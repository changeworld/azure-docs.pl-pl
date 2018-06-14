---
title: Wyświetl topologii obserwatora sieciowego Azure - Azure CLI 1.0 | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia interfejsu wiersza polecenia platformy Azure w wersji 1.0 się zapytanie o topologii sieci.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bf12e1bde56c06e496d29ad27ba3da65cd94629e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864049"
---
# <a name="view-network-watcher-topology-with-azure-cli-10"></a>Wyświetl topologią obserwatora sieciowego za pomocą interfejsu wiersza polecenia platformy Azure w wersji 1.0

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-topology-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-topology-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-topology-cli.md)
> - [Interfejs API REST](network-watcher-topology-rest.md)

Funkcja topologii sieci obserwatora zapewnia wizualną reprezentację zasobów sieciowych w ramach subskrypcji. W portalu tej wizualizacji są prezentowane automatycznie. Informacje dotyczące widoku topologii w portalu można pobrać za pomocą programu PowerShell.
Dzięki temu informacje topologii bardziej elastyczne, dane mogą być używane przez inne narzędzia do tworzenia wizualizacji.

W tym artykule wykorzystano 1.0 interfejsu wiersza polecenia platformy Azure i platform, która jest dostępna dla systemu Windows, Mac i Linux. 

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

W tym scenariuszu, należy użyć `network watcher topology` polecenia cmdlet można pobrać informacji o topologii. Istnieje również artykuł na temat [pobrać topologii sieci z interfejsu API REST](network-watcher-topology-rest.md).

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule pobiera odpowiedź topologii dla określonej grupy zasobów.

## <a name="retrieve-topology"></a>Pobrać topologii

`network watcher topology` Polecenie cmdlet pobiera topologii dla określonej grupy zasobów. Dodaj argument "--json" Aby wyświetlić oput w formacie json

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Wyniki

Wyniki zwrócone ma właściwości name "zasoby", które zawiera treści odpowiedzi json dla `network watcher topology` polecenia cmdlet.  Odpowiedź zawiera zasoby w grupie zabezpieczeń sieci i ich powiązania (to znaczy zawiera, skojarzone).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat reguł zabezpieczeń, które są stosowane do zasobów sieciowych, odwiedzając [Omówienie widoku grupy zabezpieczeń](network-watcher-security-group-view-overview.md)
