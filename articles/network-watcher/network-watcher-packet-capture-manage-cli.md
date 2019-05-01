---
title: Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia, jak zarządzać funkcji przechwytywania pakietów usługi Network Watcher przy użyciu wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7e6b1d77d002b8c1ed32a4e7adbdd1a46cf65668
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687087"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów obserwatora sieci umożliwia tworzenie sesji przechwytywania, aby śledzić ruch do i z maszyny wirtualnej. Filtry są dostarczane dla sesji przechwytywania, aby upewnić się, że Przechwytywanie ruchu, który ma. Przechwytywanie pakietów ułatwia diagnozowanie anomalie sieci w sposób reaktywny i aktywnie. Inne zastosowania obejmują zbierania statystyk sieciowych, uzyskiwanie informacji na temat włamań sieci, debugowanie komunikacja klient serwer i wiele więcej. Dzięki możliwości zdalnie wyzwalać Przechwytywanie pakietów, ta funkcja ułatwia obciążeń działających przechwytywania pakietów, ręcznie i tylko na odpowiednią maszynę, co pozwoli zaoszczędzić cenny czas.

Aby wykonać kroki opisane w tym artykule, musisz [instalowanie interfejsu wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (Azure CLI)](/cli/azure/install-azure-cli).

Ten artykuł przeprowadzi Cię przez zadania zarządzania różnych, które są aktualnie dostępne do przechwytywania pakietów.

- [**Rozpocząć przechwytywania pakietu**](#start-a-packet-capture)
- [**Zatrzymać przechwytywania pakietu**](#stop-a-packet-capture)
- [**Usuwanie przechwycenia pakietu**](#delete-a-packet-capture)
- [**Pobierz przechwytywania pakietów**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

- Wystąpienie usługi Network Watcher w regionie, w której chcesz utworzyć przechwytywania pakietów
- Maszyna wirtualna o włączeniu rozszerzenia przechwytywania pakietów.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga agenta należy uruchomić na maszynie wirtualnej. Agent jest zainstalowany jako rozszerzenie. Aby uzyskać instrukcje dotyczące rozszerzeń maszyn wirtualnych, odwiedź stronę [rozszerzeniach i funkcjach maszyn wirtualnych](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Instalowanie rozszerzenia maszyny wirtualnej

### <a name="step-1"></a>Krok 1

Uruchom `az vm extension set` polecenia cmdlet, aby zainstalować agenta przechwytywania pakietów na maszynie wirtualnej gościa.

W przypadku maszyn wirtualnych Windows:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Dla maszyn wirtualnych systemu Linux:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
```

### <a name="step-2"></a>Krok 2

Aby upewnić się, że agent jest zainstalowany, uruchom `vm extension show` polecenia cmdlet i przekaż mu nazwę maszyn wirtualnych i grupy zasobów. Sprawdź listę wyników, aby upewnić się, że agent jest zainstalowany.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Poniższy przykład jest przykładem odpowiedzi uruchamianie `az vm extension show`

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

## <a name="start-a-packet-capture"></a>Rozpocząć przechwytywania pakietu

Po zakończeniu powyższych kroków agent przechwytywania pakietów jest zainstalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Następnym krokiem jest można pobrać wystąpienia usługi Network Watcher. Nazwa usługi Network Watcher jest przekazywany do `az network watcher show` polecenia cmdlet w kroku 4.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>Krok 2

Pobierz konto magazynu. To konto magazynu jest używane do przechowywania pliku przechwytywania pakietów.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Krok 3

Filtry można ograniczyć dane, które są przechowywane przez Przechwytywanie pakietów. Poniższy przykład ustawia przechwytywania pakietów za pomocą kilku filtrów.  Pierwsze trzy filtry zbierają ruch wychodzący TCP tylko z lokalny adres IP 10.0.0.3 porty docelowe 20, 80 i 443.  Ten filtr służy do zbierania tylko ruch UDP.

```azurecli
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Poniższy przykład jest oczekiwane dane wyjściowe uruchamianie `az network watcher packet-capture create` polecenia cmdlet.

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

## <a name="get-a-packet-capture"></a>Rozpoczynanie przechwytywania pakietu

Uruchamianie `az network watcher packet-capture show-status` polecenie cmdlet pobiera stan aktualnie uruchomionej lub ukończonej przechwytywania pakietów.

```azurecli
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Poniższy przykład przedstawia dane wyjściowe z `az network watcher packet-capture show-status` polecenia cmdlet. Poniższy przykład jest po zatrzymaniu przechwytywania za pomocą Przyczynazatrzymania TimeExceeded. 

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

## <a name="stop-a-packet-capture"></a>Zatrzymać przechwytywania pakietu

Uruchamiając `az network watcher packet-capture stop` polecenia cmdlet, jeśli sesja przechwytywania jest w toku jest zatrzymana.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Polecenie cmdlet zwraca odpowiedź nie gdy uruchomiono na aktualnie uruchomionej sesji przechwytywania lub istniejącą sesję, która została już zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwycenia pakietu

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Usuwanie przechwycenia pakietu nie powoduje usunięcia plików na koncie magazynu.

## <a name="download-a-packet-capture"></a>Pobierz przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów pliku przechwytywania można przekazać do magazynu obiektów blob lub do pliku lokalnego na maszynie Wirtualnej. Lokalizacja magazynu przechwytywania pakietów jest zdefiniowany podczas tworzenia sesji. Wygodne narzędzie do nich dostęp pliki przechwytywania zapisane na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Kolejne kroki

Informacje o automatyzowaniu przechwytywania pakietów przy użyciu alertów maszyny wirtualnej, wyświetlając [tworzenie przechwytywania pakietów wyzwolonych alertów](network-watcher-alert-triggered-packet-capture.md)

Dowiedz się, czy niektóre jest dozwolony ruch do lub z maszyny Wirtualnej, odwiedzając [weryfikowanie przepływu protokołu IP z Sprawdź](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
