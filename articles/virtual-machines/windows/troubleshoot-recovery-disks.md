---
title: Użyj Windows, rozwiązywanie problemów z maszyny Wirtualnej przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyn wirtualnych Windows na platformie Azure, łącząc dysku systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 1e87704e7d8cf3c7cc21e537d36f95a97265061b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903520"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Rozwiązywanie problemów z maszyny Wirtualnej z systemem Windows, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu programu Azure PowerShell
Windows maszyny wirtualnej (VM) na platformie Azure napotka błąd rozruchu lub dysk, może być konieczne wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być Niepowodzenie aktualizacji aplikacji, która uniemożliwia maszynie Wirtualnej możliwość wykonania rozruchu pomyślnie. W tym artykule opisano, jak połączyć wirtualny dysk twardy do innej Windows maszyny Wirtualnej, aby naprawić wszystkie błędy, a następnie ponownie utworzyć oryginalną maszynę Wirtualną za pomocą programu Azure PowerShell.


## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuń maszynę Wirtualną napotyka problemy, utrzymując wirtualnych dysków twardych.
2. Dołącz i zainstaluj wirtualny dysk twardy do innej maszyny Wirtualnej Windows na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom dowolnych narzędzi w celu rozwiązywania problemów na oryginalnym wirtualnym dysku twardym.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz Maszynę wirtualną za pomocą oryginalnego wirtualnego dysku twardego.

Dla maszyny Wirtualnej używającej dysków zarządzanych, zobacz [Rozwiązywanie problemów z dysku zarządzanego maszyny Wirtualnej przez dołączenie dysku systemu operacyjnego nowy](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).


Upewnij się, że masz [najnowszą wersję programu Azure PowerShell](/powershell/azure/overview) zainstalowane i zalogować się do Twojej subskrypcji:

```powershell
Connect-AzureRmAccount
```

W poniższych przykładach należy zastąpić własnymi wartościami nazw parametrów. Przykładowe nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.


## <a name="determine-boot-issues"></a>Określenia problemów rozruchu
Możesz wyświetlić zrzut ekranu maszyny wirtualnej na platformie Azure, aby ułatwić rozwiązywanie problemów rozruchu. Ten zrzut ekranu może pomóc w ustaleniu, dlaczego maszyny Wirtualnej nie można uruchomić. Poniższy przykład pobiera zrzut ekranu z maszyny Wirtualnej Windows o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Przejrzyj zrzut ekranu, aby ustalić, dlaczego maszyny Wirtualnej nie może uruchomić. Należy pamiętać, wszystkie określone komunikaty o błędach lub kody błędów, pod warunkiem.


## <a name="view-existing-virtual-hard-disk-details"></a>Szczegółowe informacje są istniejącego wirtualnego dysku twardego
Zanim będzie możliwe dołączenie wirtualny dysk twardy do innej maszyny Wirtualnej, należy określić nazwę wirtualnego dysku twardego (VHD).

Poniższy przykład pobiera informacje dotyczące maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Wyszukaj `Vhd URI` w ramach `StorageProfile` sekcji z danych wyjściowych poprzedniego polecenia. Następujące obcięte pokazano w przykładzie danych wyjściowych `Vhd URI` pod koniec bloku kodu:

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>Usuń istniejącą maszynę Wirtualną
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy jest, gdzie są przechowywane sam system operacyjny, aplikacje i konfiguracje. Samej maszyny Wirtualnej jest po prostu metadanymi, definiują rozmiar lub lokalizację, która odwołuje się do zasobów, takich jak wirtualny dysk twardy lub karty interfejsu sieci wirtualnej (NIC). Każdy wirtualny dysk twardy ma przypisaną dzierżawę, kiedy dołączone do maszyny Wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dalszym ciągu będzie kojarzyła dysk systemu operacyjnego z maszyną Wirtualną, nawet wtedy, gdy ta maszyna wirtualna jest w stanie zatrzymania i ma cofnięty przydział dzierżawy.

Pierwszy krok, aby odzyskać maszynę Wirtualną jest nieusuwanie samego zasobu maszyny Wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny Wirtualnej możesz dołączyć wirtualny dysk twardy do innej maszyny Wirtualnej do rozwiązywania oraz usuwania błędów.

Poniższy przykład usuwa maszynę Wirtualną o nazwie `myVM` z grupy zasobów o nazwie `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Poczekaj, aż maszyny Wirtualnej zostało zakończone, usuwanie, przed dołączeniem wirtualny dysk twardy do innej maszyny Wirtualnej. Dzierżawy dla wirtualnego dysku twardego, który kojarzy ją z maszyny Wirtualnej musi zostać zwolnione, zanim będzie możliwe dołączenie wirtualny dysk twardy do innej maszyny Wirtualnej.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołącz istniejący wirtualny dysk twardy do innej maszyny Wirtualnej
W następnych kilku krokach należy użyć innej maszyny Wirtualnej na potrzeby rozwiązywania problemów. Możesz dołączyć istniejący wirtualny dysk twardy do tej maszyny Wirtualnej rozwiązywania problemów do przeglądania i edytowania zawartości na dysku. Ten proces umożliwia Popraw wszelkie błędy konfiguracji lub przejrzyj dodatkową aplikację i plikach dziennika systemowego, na przykład. Wybierz lub Utwórz inną maszynę Wirtualną do użycia na potrzeby rozwiązywania problemów.

Po dołączeniu istniejącego wirtualnego dysku twardego, należy określić adres URL dysku uzyskanego w poprzednim `Get-AzureRmVM` polecenia. Poniższy przykład dołącza istniejącego wirtualnego dysku twardego do maszyny Wirtualnej rozwiązywania problemów, o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Dodawanie dysku wymaga określenia rozmiaru dysku. Jak dołączyć istniejącego dysku, `-DiskSizeInGB` jest określony jako `$null`. Ta wartość zapewnia prawidłowo jest dołączony dysk danych i bez konieczności określenia prawdziwy rozmiar dysku danych.


## <a name="mount-the-attached-data-disk"></a>Zainstaluj dysk danych dołączony

1. Protokół RDP z maszyną wirtualną rozwiązywania problemów, przy użyciu odpowiednich poświadczeń. Poniższy przykład pobiera plik połączenia RDP dla maszyny Wirtualnej o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`i pobranie go do `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Dysk danych jest automatycznie wykryte i dołączony. Wyświetl listę dołączone woluminy w celu określenia litery dysku w następujący sposób:

    ```powershell
    Get-Disk
    ```

    Następujące przykładowe dane wyjściowe zawiera wirtualny dysk twardy podłączony dysk **2**. (Możesz również użyć `Get-Volume` do wyświetlania literę dysku):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalnym wirtualnym dysku twardym
Istniejący wirtualny dysk twardy zainstalowany można teraz wykonywać żadnej konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Gdy błędy są rozwiązywane, odinstaluj i Odłącz istniejący wirtualny dysk twardy z maszyny Wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z innej maszyny Wirtualnej, dopóki dzierżawa dołączanie wirtualnego dysku twardego do maszyny Wirtualnej rozwiązywania problemów zostanie zwolniona.

1. Z w ramach sesji protokołu RDP, odinstaluj dysk z danymi na maszynę Wirtualną odzyskiwania. Potrzebujesz numer dysku z poprzedniego `Get-Disk` polecenia cmdlet. Następnie należy użyć `Set-Disk` można ustawić dysk w trybie offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Upewnij się, dysku są teraz ustawione w trybie offline, używając `Get-Disk` ponownie. Następujące przykładowe dane wyjściowe pokazuje, że dysk jest teraz skonfigurowane w trybie offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Zamknij sesję RDP. Z poziomu sesji programu Azure PowerShell należy usunąć wirtualny dysk twardy z maszyny Wirtualnej rozwiązywania problemów.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej na podstawie oryginalnego dysku twardego
Aby utworzyć Maszynę wirtualną z oryginalnego wirtualnego dysku twardego, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Jest prawdziwy szablon JSON z łącza:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

Szablon umożliwia wdrożenie maszyny Wirtualnej w istniejącej sieci wirtualnej przy użyciu adresu URL wirtualnego dysku twardego z wcześniej polecenia. Poniższy przykład służy do wdrażania szablonu w grupie zasobów o nazwie `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Wprowadź dane w monitach dla szablonu, takie jak nazwa maszyny Wirtualnej, typ systemu operacyjnego i rozmiaru maszyny Wirtualnej. `osDiskVhdUri` Jest taka sama jak wcześniej używane podczas dołączania istniejącego wirtualnego dysku twardego do maszyny Wirtualnej rozwiązywania problemów.


## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu

Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego Diagnostyka rozruchu może nie być włączane automatycznie. Poniższy przykład umożliwia rozszerzenie diagnostyki na maszynie Wirtualnej o nazwie `myVMDeployed` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Rozwiązywanie problemów z dysku zarządzanego maszyny Wirtualnej przez dołączenie nowy dysk systemu operacyjnego
1. Zatrzymaj klientów zarządzanych dysków Windows maszynę Wirtualną.
2. [Tworzenie migawki dysku zarządzanego](snapshot-copy-managed-disk.md) dysku systemu operacyjnego, dysku zarządzanego maszyny wirtualnej.
3. [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Dołączanie dysku zarządzanego jako dysku danych maszyny wirtualnej](attach-disk-ps.md).
5. [Zmień dysk z danymi z kroku 4, na dysku systemu operacyjnego](os-disk-swap.md).

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [połączenia RDP Rozwiązywanie problemów z Maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [aplikacji Rozwiązywanie problemów z łącznością na maszynie Wirtualnej Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby uzyskać więcej informacji na temat przy użyciu usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
