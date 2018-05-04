---
title: Zarządzanie przechwytywania pakietów z obserwatora sieciowego Azure - PowerShell | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia sposób zarządzania funkcja przechwytywania pakietów obserwatora sieciowego przy użyciu programu PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 6ffb1aec91899b54a153e264e346910caee84cc0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Zarządzanie przechwytywania pakietów za pomocą Monitora sieci Azure przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [Program PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API Azure REST](network-watcher-packet-capture-manage-rest.md)

Przechwytywania pakietów obserwatora sieciowego umożliwia tworzenie sesji przechwytywania, aby śledzić ruch do i z maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytywać ruch, który ma. Przechwytywania pakietów ułatwia diagnozowanie anomalii sieci rozbudowy i aktywne. Innych celów obejmują zbieranie statystyk sieciowych, uzyskanie informacji na temat wtargnięcia sieci, aby debugować komunikacja klient serwer i o wiele więcej. Dzięki możliwości zdalnie wywołać przechwytywania pakietów, ta funkcja ułatwia obciążeń uruchomionych przechwytywania pakietów, ręcznie i tylko na odpowiednią maszynę, która zapisuje cenny czas.

Ten artykuł przedstawia za pomocą zadań zarządzania różnych, które są obecnie dostępne dla przechwytywania pakietów.

- [**Uruchom przechwytywania pakietów**](#start-a-packet-capture)
- [**Zatrzymaj przechwytywania pakietów**](#stop-a-packet-capture)
- [**Usuń przechwytywania pakietów**](#delete-a-packet-capture)
- [**Pobierz przechwytywania pakietów**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

* Wystąpienia obserwatora sieciowego w regionie, w którym chcesz utworzyć przechwytywania pakietów

* Maszyna wirtualna z rozszerzeniem przechwytywania pakietów włączone.

> [!IMPORTANT]
> Rozszerzenie maszyny wirtualnej wymaga przechwytywania pakietów `AzureNetworkWatcherExtension`. Instalowanie rozszerzenia na maszynie Wirtualnej systemu Windows można znaleźć [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Windows](../virtual-machines/windows/extensions-nwa.md) i dla maszyny Wirtualnej systemu Linux, odwiedź [rozszerzenie maszyny wirtualnej Azure sieci obserwatorów agenta dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Zainstaluj rozszerzenia maszyny Wirtualnej

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzureRmVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

Poniższy przykład pobiera rozszerzenie informacje potrzebne do uruchomienia `Set-AzureRmVMExtension` polecenia cmdlet. To polecenie cmdlet instaluje agenta przechwytywania pakietów na maszynie wirtualnej gościa.

> [!NOTE]
> `Set-AzureRmVMExtension` Polecenie cmdlet może potrwać kilka minut.

W przypadku maszyn wirtualnych systemu Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Dla maszyn wirtualnych systemu Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzureRmVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
````

Poniższy przykład jest odpowiedź oznaczająca Powodzenie po uruchomieniu `Set-AzureRmVMExtension` polecenia cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Aby upewnić się, że agent jest zainstalowany, należy uruchomić `Get-AzureRmVMExtension` polecenia cmdlet i przekaż go nazwę maszyny wirtualnej i nazwa rozszerzenia.

```powershell
Get-AzureRmVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Poniższy przykład jest przykładem uruchamianie odpowiedzi `Get-AzureRmVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Uruchom przechwytywania pakietów

Po zakończeniu powyższych kroków agent przechwytywania pakietów jest zainstalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Następnym krokiem jest w celu pobrania wystąpienia obserwatora sieciowego. Ta zmienna jest przekazywana do `New-AzureRmNetworkWatcherPacketCapture` polecenia cmdlet w kroku 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>Krok 2

Pobierz konta magazynu. To konto magazynu jest używany do przechowywania pliku przechwytywania pakietów.

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Krok 3

Można używać filtrów w celu ograniczenia danych, który jest przechowywany przez przechwytywania pakietów. Poniższy przykład przedstawia dwa filtry.  Jeden filtr zbiera ruch wychodzący TCP tylko lokalny adres IP 10.0.0.3 do porty docelowe 20, 80 i 443.  Drugi filtr zbiera tylko ruch UDP.

```powershell
$filter1 = New-AzureRmPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzureRmPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Można zdefiniować wiele filtrów dla przechwytywania pakietów.

### <a name="step-4"></a>Krok 4

Uruchom `New-AzureRmNetworkWatcherPacketCapture` pobrać polecenia cmdlet, aby rozpocząć proces przechwytywania pakietów, przekazywanie wymagane wartości w poprzednich krokach.
```powershell

New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Poniższy przykład jest oczekiwane dane wyjściowe uruchamianie `New-AzureRmNetworkWatcherPacketCapture` polecenia cmdlet.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Pobierz przechwytywania pakietów

Uruchomiona `Get-AzureRmNetworkWatcherPacketCapture` polecenie cmdlet pobiera stan przechwytywania pakietów aktualnie uruchomione lub zostało zakończone.

```powershell
Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Poniższy przykład jest wynikiem `Get-AzureRmNetworkWatcherPacketCapture` polecenia cmdlet. Poniższy przykład jest po zakończeniu przechwytywania. Wartość PacketCaptureStatus została zatrzymana z StopReason TimeExceeded. Ta wartość pokazuje, że przechwytywania pakietów zakończyła się pomyślnie i jego uruchomienia.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Zatrzymaj przechwytywania pakietów

Uruchamiając `Stop-AzureRmNetworkWatcherPacketCapture` polecenia cmdlet, jeśli sesja przechwytywania jest w toku jest zatrzymana.

```powershell
Stop-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Polecenie cmdlet nie zwraca żadnej odpowiedzi podczas uruchomionego na aktualnie uruchomionych sesji przechwytywania lub istniejącej sesji, która już została zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuń przechwytywania pakietów

```powershell
Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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

Znajdź, jeśli niektórych ruch jest dozwolony w orr poza maszyny Wirtualnej, odwiedzając [Sprawdź przepływ Sprawdź IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->














