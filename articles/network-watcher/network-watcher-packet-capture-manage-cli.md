---
title: Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher — azure cli | Dokumenty firmy Microsoft
description: Na tej stronie wyjaśniono, jak zarządzać funkcją przechwytywania pakietów funkcji Network Watcher przy użyciu interfejsu wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7a69610d1ac176354a9d7e388a12ccc7f064d848
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382719"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Zarządzanie przechwytywaniami pakietów za pomocą usługi Azure Network Watcher przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów Obserwatora sieciowego umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytujesz tylko ruch, który chcesz. Przechwytywanie pakietów pomaga diagnozować anomalie sieci zarówno reaktywnie, jak i proaktywnie. Inne zastosowania obejmują zbieranie statystyk sieciowych, uzyskiwanie informacji o włamaniach do sieci, debugowanie komunikacji klient-serwer i wiele więcej. Dzięki możliwości zdalnego wyzwalania przechwytywania pakietów, ta funkcja zmniejsza obciążenie związane z ręcznym uruchamianiem przechwytywania pakietów i na żądanym komputerze, co pozwala zaoszczędzić cenny czas.

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejs wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows (Azure CLI)](/cli/azure/install-azure-cli).

W tym artykule oprowadza cię przez różne zadania zarządzania, które są obecnie dostępne do przechwytywania pakietów.

- [**Rozpoczynanie przechwytywania pakietów**](#start-a-packet-capture)
- [**Zatrzymywanie przechwytywania pakietów**](#stop-a-packet-capture)
- [**Usuwanie przechwytywania pakietów**](#delete-a-packet-capture)
- [**Pobieranie przechwytywania pakietów**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz następujące zasoby:

- Wystąpienie Obserwatora sieciowego w regionie, w który chcesz utworzyć przechwytywanie pakietów
- Maszyna wirtualna z włączonym rozszerzeniem przechwytywania pakietów.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga agenta do uruchomienia na maszynie wirtualnej. Agent jest zainstalowany jako rozszerzenie. Aby uzyskać instrukcje dotyczące rozszerzeń maszyn wirtualnych, odwiedź stronę [Rozszerzenia i funkcje maszyny wirtualnej](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Instalowanie rozszerzenia maszyny wirtualnej

### <a name="step-1"></a>Krok 1

Uruchom `az vm extension set` polecenie, aby zainstalować agenta przechwytywania pakietów na maszynie wirtualnej gościa.

W przypadku maszyn wirtualnych z systemem Windows:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Dla maszyn wirtualnych systemu Linux:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Krok 2

Aby upewnić się, że `vm extension show` agent jest zainstalowany, uruchom polecenie i przekaż go nazwę grupy zasobów i maszyny wirtualnej. Sprawdź wynikową listę, aby upewnić się, że agent jest zainstalowany.

W przypadku maszyn wirtualnych z systemem Windows:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Dla maszyn wirtualnych systemu Linux:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Poniższy przykład jest przykładem odpowiedzi z uruchomienia`az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Rozpoczynanie przechwytywania pakietów

Po zakończeniu poprzednich kroków agent przechwytywania pakietów jest instalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Pobieranie konta magazynu. To konto magazynu służy do przechowywania pliku przechwytywania pakietów.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Krok 2

W tym momencie można przystąpić do tworzenia przechwytywania pakietów.  Najpierw przyjrzyjmy się parametrom, które można skonfigurować. Filtry są jednym z takich parametrów, które mogą być używane do ograniczania danych przechowywanych przez przechwytywanie pakietów. W poniższym przykładzie konfiguruje przechwytywanie pakietów za pomocą kilku filtrów.  Pierwsze trzy filtry zbierają wychodzący ruch TCP tylko z lokalnego adresu IP 10.0.0.3 do portów docelowych 20, 80 i 443.  Ostatni filtr zbiera tylko ruch UDP.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Poniższy przykład jest oczekiwane `az network watcher packet-capture create` dane wyjściowe z uruchomienia polecenia.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Uzyskaj przechwytywanie pakietów

Uruchomienie `az network watcher packet-capture show-status` polecenia, pobiera stan aktualnie uruchomionego lub zakończonego przechwytywania pakietów.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Poniższy przykład jest dane `az network watcher packet-capture show-status` wyjściowe z polecenia. Poniższy przykład jest, gdy przechwytywanie jest zatrzymana, z StopReason TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Zatrzymywanie przechwytywania pakietów

Uruchamiając `az network watcher packet-capture stop` polecenie, jeśli trwa sesja przechwytywania, jest zatrzymana.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Polecenie nie zwraca odpowiedzi po uruchomieniu aktualnie uruchomionej sesji przechwytywania lub istniejącej sesji, która została już zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwytywania pakietów

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Usunięcie przechwytywania pakietów nie powoduje usunięcia pliku z konta magazynu.

## <a name="download-a-packet-capture"></a>Pobieranie przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów plik przechwytywania można przekazać do magazynu obiektów blob lub do pliku lokalnego na maszynie Wirtualnej. Lokalizacja przechowywania przechwytywania pakietów jest definiowana podczas tworzenia sesji. Wygodnym narzędziem dostępu do tych plików przechwytywania zapisanych na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:https://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zautomatyzować przechwytywanie pakietów za pomocą alertów maszyn wirtualnych, przeglądając [Tworzenie wyzwalanego alertu przechwytywania pakietów](network-watcher-alert-triggered-packet-capture.md)

Sprawdzanie, czy określony ruch jest dozwolony w maszynie wirtualnej lub poza niej, odwiedzając weryfikację [przepływu adresu IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
