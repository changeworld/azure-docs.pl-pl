---
title: Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher — PowerShell | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia, jak zarządzać funkcji przechwytywania pakietów usługi Network Watcher przy użyciu programu PowerShell
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
ms.openlocfilehash: 267b2c375ef9672c8e5bd7cb8280b4dd40dbcd0d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045547"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Zarządzanie przechwytywaniem pakietów przy użyciu usługi Azure Network Watcher przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-packet-capture-manage-cli.md)
> - [Interfejs API REST platformy Azure](network-watcher-packet-capture-manage-rest.md)

Przechwytywanie pakietów obserwatora sieci umożliwia tworzenie sesji przechwytywania, aby śledzić ruch do i z maszyny wirtualnej. Filtry są dostarczane dla sesji przechwytywania, aby upewnić się, że Przechwytywanie ruchu, który ma. Przechwytywanie pakietów ułatwia diagnozowanie anomalie sieci w sposób reaktywny i aktywnie. Inne zastosowania obejmują zbierania statystyk sieciowych, uzyskiwanie informacji na temat włamań sieci, debugowanie komunikacja klient serwer i wiele więcej. Dzięki możliwości zdalnie wyzwalać Przechwytywanie pakietów, ta funkcja ułatwia obciążeń działających przechwytywania pakietów, ręcznie i tylko na odpowiednią maszynę, co pozwoli zaoszczędzić cenny czas.

Ten artykuł przeprowadzi Cię przez zadania zarządzania różnych, które są aktualnie dostępne do przechwytywania pakietów.

- [**Rozpocząć przechwytywania pakietu**](#start-a-packet-capture)
- [**Zatrzymać przechwytywania pakietu**](#stop-a-packet-capture)
- [**Usuwanie przechwycenia pakietu**](#delete-a-packet-capture)
- [**Pobierz przechwytywania pakietów**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz następujące zasoby:

* Wystąpienie usługi Network Watcher w regionie, w której chcesz utworzyć przechwytywania pakietów

* Maszyna wirtualna o włączeniu rozszerzenia przechwytywania pakietów.

> [!IMPORTANT]
> Przechwytywanie pakietów wymaga rozszerzenia maszyny wirtualnej `AzureNetworkWatcherExtension`. Instalowanie rozszerzenia na maszynie Wirtualnej Windows można znaleźć [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla Windows](../virtual-machines/windows/extensions-nwa.md) i maszyny Wirtualnej systemu Linux można znaleźć pod adresem [Agent usługi Azure Network Watcher rozszerzenie maszyny wirtualnej dla systemu Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalowanie rozszerzenia maszyny wirtualnej

### <a name="step-1"></a>Krok 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Krok 2

Poniższy przykład umożliwia pobranie informacji o rozszerzeniu potrzebnych do uruchomienia `Set-AzVMExtension` polecenia cmdlet. To polecenie cmdlet instaluje agenta przechwytywania pakietów na maszynie wirtualnej gościa.

> [!NOTE]
> `Set-AzVMExtension` Polecenie cmdlet może potrwać kilka minut.

W przypadku maszyn wirtualnych Windows:

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

Poniższy przykład jest pomyślnej odpowiedzi po uruchomieniu `Set-AzVMExtension` polecenia cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Krok 3

Aby upewnić się, że agent jest zainstalowany, uruchom `Get-AzVMExtension` polecenia cmdlet i przekaż go nazwę maszyny wirtualnej i nazwę rozszerzenia.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Poniższy przykład jest przykładem odpowiedzi uruchamianie `Get-AzVMExtension`

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

## <a name="start-a-packet-capture"></a>Rozpocząć przechwytywania pakietu

Po zakończeniu powyższych kroków agent przechwytywania pakietów jest zainstalowany na maszynie wirtualnej.

### <a name="step-1"></a>Krok 1

Następnym krokiem jest można pobrać wystąpienia usługi Network Watcher. Ta zmienna jest przekazywana do `New-AzNetworkWatcherPacketCapture` polecenia cmdlet w kroku 4.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName  
```

### <a name="step-2"></a>Krok 2

Pobierz konto magazynu. To konto magazynu jest używane do przechowywania pliku przechwytywania pakietów.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Krok 3

Filtry można ograniczyć dane, które są przechowywane przez Przechwytywanie pakietów. Poniższy przykład definiuje dwa filtry.  Jeden filtr tworzy wychodzący ruch TCP na podstawie lokalny adres IP 10.0.0.3 do portów docelowych, 20, 80 i 443.  Drugi filtr służy do zbierania tylko ruch UDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Można zdefiniować wiele filtrów dla przechwytywania pakietów.

### <a name="step-4"></a>Krok 4

Uruchom `New-AzNetworkWatcherPacketCapture` polecenia cmdlet, aby rozpocząć proces przechwytywania pakietów, przekazywanie wymagane wartości są pobierane w poprzednich krokach.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Poniższy przykład jest oczekiwane dane wyjściowe uruchamianie `New-AzNetworkWatcherPacketCapture` polecenia cmdlet.

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

## <a name="get-a-packet-capture"></a>Rozpoczynanie przechwytywania pakietu

Uruchamianie `Get-AzNetworkWatcherPacketCapture` polecenie cmdlet pobiera stan aktualnie uruchomionej lub ukończonej przechwytywania pakietów.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Poniższy przykład przedstawia dane wyjściowe z `Get-AzNetworkWatcherPacketCapture` polecenia cmdlet. Poniższy przykład jest po ukończeniu przechwytywania. Za pomocą Przyczynazatrzymania TimeExceeded wartość PacketCaptureStatus został zatrzymany. Ta wartość pokazuje, że przechwycenie pakietu zakończyło się pomyślnie, a jego uruchomienia.
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

## <a name="stop-a-packet-capture"></a>Zatrzymać przechwytywania pakietu

Uruchamiając `Stop-AzNetworkWatcherPacketCapture` polecenia cmdlet, jeśli sesja przechwytywania jest w toku jest zatrzymana.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Polecenie cmdlet zwraca odpowiedź nie gdy uruchomiono na aktualnie uruchomionej sesji przechwytywania lub istniejącą sesję, która została już zatrzymana.

## <a name="delete-a-packet-capture"></a>Usuwanie przechwycenia pakietu

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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














