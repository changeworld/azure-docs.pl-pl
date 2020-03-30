---
title: Zarządzanie przechwytywaniem pakietów — usługa Azure PowerShell
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać funkcją przechwytywania pakietów funkcji Network Watcher za pomocą programu PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 06263f85f7d6ad6cc80724baab01124833498739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129657"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Zarządzanie przechwytywaniem pakietów za pomocą usługi Azure Network Watcher przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów Obserwatora sieciowego umożliwia tworzenie sesji przechwytywania w celu śledzenia ruchu do i z maszyny wirtualnej. Filtry są dostępne dla sesji przechwytywania, aby upewnić się, że przechwytujesz tylko ruch, który chcesz. Przechwytywanie pakietów pomaga diagnozować anomalie sieci zarówno reaktywnie, jak i proaktywnie. Inne zastosowania obejmują zbieranie statystyk sieciowych, uzyskiwanie informacji o włamaniach do sieci, debugowanie komunikacji klient-serwer i wiele więcej. Dzięki możliwości zdalnego wyzwalania przechwytywania pakietów, ta funkcja zmniejsza obciążenie związane z ręcznym uruchamianiem przechwytywania pakietów i na żądanym komputerze, co pozwala zaoszczędzić cenny czas.

W tym artykule oprowadza cię przez różne zadania zarządzania, które są obecnie dostępne do przechwytywania pakietów.

- [**Rozpoczynanie przechwytywania pakietów**](#start-a-packet-capture)
- [**Zatrzymywanie przechwytywania pakietów**](#stop-a-packet-capture)
- [**Usuwanie przechwytywania pakietów**](#delete-a-packet-capture)
- [**Pobieranie przechwytywania pakietów**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz następujące zasoby:

* Wystąpienie Obserwatora sieciowego w regionie, w który chcesz utworzyć przechwytywanie pakietów

* Maszyna wirtualna z włączonym rozszerzeniem przechwytywania pakietów.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga `AzureNetworkWatcherExtension`rozszerzenia maszyny wirtualnej . Aby zainstalować rozszerzenie na maszynie Wirtualnej systemu Windows odwiedź [rozszerzenie maszyny wirtualnej usługi Azure Network Watcher Agent dla systemu Windows](../virtual-machines/windows/extensions-nwa.md) i maszyny wirtualnej z systemem Linux, odwiedź rozszerzenie maszyny [wirtualnej usługi Azure Network Watcher Agent dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalowanie rozszerzenia maszyny wirtualnej

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

W poniższym przykładzie pobiera informacje `Set-AzVMExtension` o rozszerzeniu potrzebne do uruchomienia polecenia cmdlet. To polecenie cmdlet instaluje agenta przechwytywania pakietów na maszynie wirtualnej gościa.

> [!NOTE]
> Polecenie `Set-AzVMExtension` cmdlet może potrwać kilka minut.

W przypadku maszyn wirtualnych z systemem Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Dla maszyn wirtualnych systemu Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Poniższy przykład jest pomyślną `Set-AzVMExtension` odpowiedzią po uruchomieniu polecenia cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Aby upewnić się, że `Get-AzVMExtension` agent jest zainstalowany, uruchom polecenie cmdlet i przekaż go nazwę maszyny wirtualnej i nazwę rozszerzenia.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Poniższy przykład jest przykładem odpowiedzi z uruchomienia`Get-AzVMExtension`

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

## <a name="start-a-packet-capture"></a>Rozpoczynanie przechwytywania pakietów

Po zakończeniu poprzednich kroków agent przechwytywania pakietów jest instalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Następnym krokiem jest pobranie wystąpienia Obserwatora sieci. Ta zmienna jest `New-AzNetworkWatcherPacketCapture` przekazywana do polecenia cmdlet w kroku 4.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Krok 2

Pobieranie konta magazynu. To konto magazynu służy do przechowywania pliku przechwytywania pakietów.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Krok 3

Filtry mogą być używane do ograniczania danych przechowywanych przez przechwytywanie pakietów. W poniższym przykładzie konfiguruje dwa filtry.  Jeden filtr zbiera wychodzący ruch TCP tylko z lokalnego adresu IP 10.0.0.3 do portów docelowych 20, 80 i 443.  Drugi filtr zbiera tylko ruch UDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Wiele filtrów można zdefiniować dla przechwytywania pakietów.

### <a name="step-4"></a>Krok 4

Uruchom `New-AzNetworkWatcherPacketCapture` polecenie cmdlet, aby rozpocząć proces przechwytywania pakietów, przekazując wymagane wartości pobrane w poprzednich krokach.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Poniższy przykład jest oczekiwane `New-AzNetworkWatcherPacketCapture` dane wyjściowe z uruchamiania polecenia cmdlet.

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

## <a name="get-a-packet-capture"></a>Uzyskaj przechwytywanie pakietów

Uruchomienie `Get-AzNetworkWatcherPacketCapture` polecenia cmdlet, pobiera stan aktualnie uruchomionego lub zakończonego przechwytywania pakietów.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Poniższy przykład jest dane `Get-AzNetworkWatcherPacketCapture` wyjściowe z polecenia cmdlet. Poniższy przykład jest po zakończeniu przechwytywania. PacketCaptureStatus wartość jest zatrzymana, z StopReason TimeExceeded. Ta wartość pokazuje, że przechwytywanie pakietów zakończyło się pomyślnie i uruchomiono swój czas.
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

## <a name="stop-a-packet-capture"></a>Zatrzymywanie przechwytywania pakietów

Uruchamiając `Stop-AzNetworkWatcherPacketCapture` polecenie cmdlet, jeśli trwa sesja przechwytywania, jest ono zatrzymane.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Polecenie cmdlet zwraca brak odpowiedzi po uruchomieniu aktualnie uruchomionej sesji przechwytywania lub istniejącej sesji, która została już zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwytywania pakietów

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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














