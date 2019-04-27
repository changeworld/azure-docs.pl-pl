---
title: Rozwiązywanie problemów z połączeniami w usłudze Azure Network Watcher — wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z połączenia Rozwiązywanie problemów z możliwości usługi Azure Network Watcher przy użyciu wiersza polecenia platformy Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: kumud
ms.openlocfilehash: a44f46a01bd0c0530ce57ad65464c10a4d2617f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60624075"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-cli"></a>Rozwiązywanie problemów z połączeniami w usłudze Azure Network Watcher przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-connectivity-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-connectivity-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-connectivity-rest.md)

Dowiedz się, jak używać połączenia Rozwiązywanie problemów, aby sprawdzić, czy można nawiązać bezpośrednie połączenie TCP z maszyny wirtualnej do danego punktu końcowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

* Wystąpienie usługi Network Watcher w regionie, do których użytkownik chce Rozwiązywanie problemów z połączeniem.
* Rozwiązywanie problemów z połączeniami z maszyn wirtualnych.

> [!IMPORTANT]
> Rozwiązywanie problemów z połączeniami wymaga, że maszyna wirtualna, rozwiązywanie problemów z z ma `AzureNetworkWatcherExtension` zainstalowane rozszerzenie maszyny Wirtualnej. Instalowanie rozszerzenia na maszynie Wirtualnej Windows można znaleźć [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i maszyny Wirtualnej systemu Linux można znaleźć pod adresem [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozszerzenie nie jest wymagany dla docelowego punktu końcowego.

## <a name="check-connectivity-to-a-virtual-machine"></a>Sprawdź łączność z maszyną wirtualną

W tym przykładzie służy do sprawdzania łączności do docelowej maszyny wirtualnej za pośrednictwem portu 80.

### <a name="example"></a>Przykład

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Odpowiedź

Jest następującą odpowiedź z poprzedniego przykładu.  W tej odpowiedzi `ConnectionStatus` jest **informujący**. Widać, że wszystkie sondy wysyłane nie powiodło się. Połączenie nie powiodło się na urządzenie wirtualne, ze względu na skonfigurowane przez użytkownika `NetworkSecurityRule` o nazwie **UserRule_Port80**, jest skonfigurowana do blokowania ruchu przychodzącego na porcie 80. Te informacje mogą służyć do badania problemów z połączeniem.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "bb01d336-d881-4808-9fbc-72f091974d68",
      "issues": [],
      "nextHopIds": [
        "f8b074e9-9980-496b-a35e-619f9bcbf648"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "10.1.2.4",
      "id": "f8b074e9-9980-496b-a35e-619f9bcbf648",
      "issues": [],
      "nextHopIds": [
        "8a5857f3-6ab8-4b11-b9bf-a046d66b8696"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fw
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.3.4",
      "id": "8a5857f3-6ab8-4b11-b9bf-a046d66b8696",
      "issues": [
        {
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule"
        }
      ],
      "nextHopIds": [
        "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/au
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.4.4",
      "id": "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/db
Nic0/ipConfigurations/ipconfig1",
      "type": "VnetLocal"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="validate-routing-issues"></a>Sprawdź poprawność problemów z routingiem

W tym przykładzie służy do sprawdzania łączności między maszyną wirtualną i zdalnego punktu końcowego.

### <a name="example"></a>Przykład

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Odpowiedź

W poniższym przykładzie `connectionStatus` jest przedstawiana w postaci **informujący**. W `hops` uzyskać więcej informacji, możesz zobaczyć w obszarze `issues` ruch został zablokowany ze względu na `UserDefinedRoute`.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "f2cb1868-2049-4839-b8ed-57a480d06f95",
      "issues": [
        {
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute"
        }
      ],
      "nextHopIds": [
        "da4022db-0ab0-48c4-a507-dd4c03561ca5"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "13.107.21.200",
      "id": "da4022db-0ab0-48c4-a507-dd4c03561ca5",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Unknown",
      "type": "Destination"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="check-website-latency"></a>Czas oczekiwania na sprawdzenie witryny sieci Web

Poniższy przykład służy do sprawdzania łączności z witryną sieci Web.

### <a name="example"></a>Przykład

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://bing.com --dest-port 80
```

### <a name="response"></a>Odpowiedź

Następującą odpowiedź zawiera `connectionStatus` jest wyświetlany jako **osiągalne**. Gdy połączenie zostanie nawiązane, znajdują się wartości czasu oczekiwania.

```json
{
  "avgLatencyInMs": 2,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "639c2d19-e163-4dfd-8737-5018dd1168ae",
      "issues": [],
      "nextHopIds": [
        "fd43a6e7-c758-4f48-90aa-8db99105a4a3"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "204.79.197.200",
      "id": "fd43a6e7-c758-4f48-90aa-8db99105a4a3",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Sprawdź łączność z punktu końcowego magazynu

Poniższy przykład służy do sprawdzania łączności z maszyny wirtualnej na koncie magazynu w blogu.

### <a name="example"></a>Przykład

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Odpowiedź

Następujący kod json jest przykładową odpowiedź uruchomienie poprzedniego polecenia cmdlet. Jak kontrola zakończy się pomyślnie, `connectionStatus` właściwości jest wyświetlana jako **osiągalne**.  Znajdują się szczegółowe informacje dotyczące liczby przeskoków wymagany do osiągnięcia obiektem blob storage i opóźnienie.

```json
{
  "avgLatencyInMs": 1,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "5136acff-bf26-4c93-9966-4edb7dd40353",
      "issues": [],
      "nextHopIds": [
        "f8d958b7-3636-4d63-9441-602c1eb2fd56"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "1.2.3.4",
      "id": "f8d958b7-3636-4d63-9441-602c1eb2fd56",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="next-steps"></a>Kolejne kroki

Informacje o automatyzowaniu przechwytywania pakietów przy użyciu alertów maszyny wirtualnej, wyświetlając [tworzenie przechwytywania pakietów wyzwolonych alertów](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, czy niektóre jest dozwolony ruch do lub z maszyny Wirtualnej, odwiedzając [weryfikowanie przepływu protokołu IP z Sprawdź](diagnose-vm-network-traffic-filtering-problem.md)
