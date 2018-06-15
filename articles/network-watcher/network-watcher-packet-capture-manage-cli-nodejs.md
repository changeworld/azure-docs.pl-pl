---
title: Zarządzanie przechwytywania pakietów z obserwatora sieciowego Azure - Azure CLI 1.0 | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia sposób zarządzania funkcja przechwytywania pakietów obserwatora sieciowego przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c74d1a94971495f7cd5f5bed42b33869fa9710d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185389"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-10"></a>Zarządzanie przechwytywania pakietów za pomocą Monitora sieci Azure przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API Azure REST](network-watcher-packet-capture-manage-rest.md)

Przechwytywania pakietów obserwatora sieciowego umożliwia tworzenie sesji przechwytywania, aby śledzić ruch do i z maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytywać ruch, który ma. Przechwytywania pakietów ułatwia diagnozowanie anomalii sieci rozbudowy i aktywne. Innych celów obejmują zbieranie statystyk sieciowych, uzyskanie informacji na temat wtargnięcia sieci, aby debugować komunikacja klient serwer i o wiele więcej. Dzięki możliwości zdalnie wywołać przechwytywania pakietów, ta funkcja ułatwia obciążeń uruchomionych przechwytywania pakietów, ręcznie i tylko na odpowiednią maszynę, która zapisuje cenny czas.

W tym artykule wykorzystano 1.0 interfejsu wiersza polecenia platformy Azure i platform, która jest dostępna dla systemu Windows, Mac i Linux.

Ten artykuł przedstawia za pomocą zadań zarządzania różnych, które są obecnie dostępne dla przechwytywania pakietów.

- [**Uruchom przechwytywania pakietów**](#start-a-packet-capture)
- [**Zatrzymaj przechwytywania pakietów**](#stop-a-packet-capture)
- [**Usuń przechwytywania pakietów**](#delete-a-packet-capture)
- [**Pobierz przechwytywania pakietów**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

- Wystąpienia obserwatora sieciowego w regionie, w którym chcesz utworzyć przechwytywania pakietów
- Maszyna wirtualna z rozszerzeniem przechwytywania pakietów włączone.

> [!IMPORTANT]
> Przechwytywania pakietów wymaga agenta należy uruchomić na maszynie wirtualnej. Agent jest zainstalowany jako rozszerzenie. Aby uzyskać instrukcje dotyczące rozszerzenia maszyny Wirtualnej, odwiedź stronę [rozszerzenia maszyn wirtualnych i funkcji](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Zainstaluj rozszerzenia maszyny Wirtualnej

### <a name="step-1"></a>Krok 1

Uruchom `azure vm extension set` polecenia cmdlet, aby zainstalować agenta przechwytywania pakietów na maszynie wirtualnej gościa.

W przypadku maszyn wirtualnych systemu Windows:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

Dla maszyn wirtualnych systemu Linux:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>Krok 2

Aby upewnić się, że agent jest zainstalowany, należy uruchomić `vm extension get` polecenia cmdlet i przekaż go nazwy maszyn wirtualnych i grupy zasobów. Sprawdź listę wynikowy, aby upewnić się, że agent jest zainstalowany.

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

Poniższy przykład jest przykładem uruchamianie odpowiedzi `azure vm extension get`

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                        Version  State
data:    ------------------------------  -----------------------     -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentWindows  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>Uruchom przechwytywania pakietów

Po zakończeniu powyższych kroków agent przechwytywania pakietów jest zainstalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Następnym krokiem jest w celu pobrania wystąpienia obserwatora sieciowego. Ta zmienna jest przekazywana do `network watcher show` polecenia cmdlet w kroku 4.

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>Krok 2

Pobierz konta magazynu. To konto magazynu jest używany do przechowywania pliku przechwytywania pakietów.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Krok 3

Można używać filtrów w celu ograniczenia danych, który jest przechowywany przez przechwytywania pakietów. Poniższy przykład powoduje ustawienie przechwytywania pakietów z kilka filtrów.  Pierwsze trzy filtry Zbierz ruch wychodzący TCP tylko lokalny adres IP 10.0.0.3 do porty docelowe 20, 80 i 443.  Ten filtr zbiera tylko ruch UDP.

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Można zdefiniować wiele filtrów dla przechwytywania pakietów. Jeśli używasz struktury złożonych filtrów, lepiej jest używać filtrów w formacie json, aby uniknąć błędów składni. Na przykład użyć flagi "-r" (zamiast "-f"), a następnie przekaż lokalizację pliku json, który zawiera następujące filtry:

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


Poniższy przykład jest oczekiwane dane wyjściowe uruchamianie `network watcher packet-capture create` polecenia cmdlet.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>Pobierz przechwytywania pakietów

Uruchomiona `network watcher packet-capture show` polecenie cmdlet pobiera stan przechwytywania pakietów aktualnie uruchomione lub zostało zakończone.

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

Poniższy przykład jest wynikiem `network watcher packet-capture show` polecenia cmdlet. Poniższy przykład jest po zakończeniu przechwytywania. Wartość PacketCaptureStatus została zatrzymana z StopReason TimeExceeded. Ta wartość pokazuje, że przechwytywania pakietów zakończyła się pomyślnie i jego uruchomienia.

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>Zatrzymaj przechwytywania pakietów

Uruchamiając `network watcher packet-capture stop` polecenia cmdlet, jeśli sesja przechwytywania jest w toku jest zatrzymana.

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> Polecenie cmdlet nie zwraca żadnej odpowiedzi podczas uruchomionego na aktualnie uruchomionych sesji przechwytywania lub istniejącej sesji, która już została zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuń przechwytywania pakietów

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> Usunięcie przechwytywania pakietów nie powoduje usunięcia plików na koncie magazynu.

## <a name="download-a-packet-capture"></a>Pobierz przechwytywania pakietów

Po zakończeniu sesji przechwytywania pakietów, plik przechwytywania można przekazać do magazynu obiektów blob lub do pliku lokalnego na maszynie Wirtualnej. Lokalizacja magazynu przechwytywania pakietów jest definiowany podczas tworzenia sesji. Wygodne narzędzie one dostęp do plików przechwytywania na koncie magazynu jest Microsoft Azure Eksploratora usługi Storage, który można pobrać tutaj:  http://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak można zautomatyzować przechwytywania pakietów z alertami maszyny wirtualnej, wyświetlając [utworzyć przechwytywania alertów wyzwalanych pakietów](network-watcher-alert-triggered-packet-capture.md)

Znajdź, jeśli niektórych ruch jest dozwolony w lub z maszyny Wirtualnej, odwiedzając [Sprawdź przepływ Sprawdź IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
