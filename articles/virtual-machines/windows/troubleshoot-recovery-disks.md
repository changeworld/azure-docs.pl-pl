---
title: Używanie okien, rozwiązywanie problemów z maszyny Wirtualnej przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyny Wirtualnej systemu Windows na platformie Azure, łącząc dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu programu Azure PowerShell
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
ms.openlocfilehash: cbb9a47b878471e6efd5f4e280f44c6c8fe78df4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Rozwiązywanie problemów z maszyny Wirtualnej systemu Windows, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu programu Azure PowerShell
Maszyny wirtualnej systemu Windows (VM) na platformie Azure napotkał błąd podczas rozruchu lub dysk, konieczne może wykonać kroki rozwiązywania problemów na wirtualnym dysku twardym, do samej siebie. Typowym przykładem jest aktualizacja aplikacji nie powiodło się, który uniemożliwia było pomyślnie uruchomić maszynę Wirtualną. Ten artykuł zawiera szczegóły dotyczące sposobu używania programu Azure PowerShell do wirtualnego dysku twardego nawiązać połączenia z innej maszyny Wirtualnej systemu Windows, usuń wszelkie błędy, a następnie ponownie utwórz oryginalna maszyna wirtualna.


## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuwanie maszyny Wirtualnej, wystąpią problemy, utrzymując wirtualnych dysków twardych.
2. Dołączanie i zainstalować wirtualny dysk twardy do innej maszyny Wirtualnej systemu Windows na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytowanie plików lub uruchamianie narzędzi do rozwiązywania problemów z na oryginalny wirtualny dysk twardy.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz maszynę Wirtualną przy użyciu oryginalny wirtualny dysk twardy.

Upewnij się, że masz [najnowsze programu Azure PowerShell](/powershell/azure/overview) zainstalowane i zalogowany do subskrypcji:

```powershell
Login-AzureRMAccount
```

W poniższych przykładach zastąpić parametr własne wartości. Przykład nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.


## <a name="determine-boot-issues"></a>Określenie zagadnień rozruchu
Zrzut ekranu maszyny wirtualnej można wyświetlać na platformie Azure, aby ułatwić rozwiązywanie problemów rozruchu. Ten zrzut ekranu może ułatwić zidentyfikowanie przyczyny maszyny Wirtualnej nie można uruchomić. Poniższy przykład pobiera zrzut ekranu z maszyny Wirtualnej systemu Windows o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Przejrzyj zrzut ekranu, aby ustalić, dlaczego niepowodzenie maszyny Wirtualnej do rozruchu. Należy pamiętać, wszystkie określone komunikaty o błędach lub podać kody błędów.


## <a name="view-existing-virtual-hard-disk-details"></a>Szczegóły istniejącego wirtualnego dysku twardego
Przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej, należy określić nazwę wirtualnego dysku twardego (VHD).

Poniższy przykład pobiera informacje o Maszynie wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Wyszukaj `Vhd URI` w `StorageProfile` sekcji z danych wyjściowych poprzednie polecenie. Następujące obcięty przykładzie danych wyjściowych `Vhd URI` pod koniec bloku kodu:

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
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy jest, gdzie są przechowywane sam system operacyjny, aplikacje i konfiguracje. Samej maszyny Wirtualnej są tylko metadane, który definiuje rozmiaru lub lokalizacji i odwołuje się do zasobów, takich jak wirtualny dysk twardy lub sieć wirtualna karta sieciowa (NIC). Każdy wirtualny dysk twardy ma dzierżawę przypisana w momencie dołączony do maszyny Wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dzierżawy w dalszym ciągu skojarzyć dysk systemu operacyjnego z maszyny Wirtualnej, nawet w przypadku tej maszyny Wirtualnej w stanie zatrzymania i deallocated.

Pierwszy krok w celu odzyskania maszyny Wirtualnej jest usunąć samego zasobu maszyny Wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny Wirtualnej, możesz dołączyć wirtualnego dysku twardego do innej maszyny Wirtualnej do rozwiązywania oraz usuwania błędów.

Poniższy przykład powoduje usunięcie maszyny Wirtualnej o nazwie `myVM` z grupy zasobów o nazwie `myResourceGroup`:

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Poczekaj na Maszynie wirtualnej zakończył, usuwanie przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej. Dzierżawy na wirtualnym dysku twardym, który kojarzy go z maszyny Wirtualnej musi zostać zwolniony przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołączanie istniejącego wirtualnego dysku twardego do innej maszyny Wirtualnej
Dla następnych kilku krokach możesz użyć innej maszyny Wirtualnej na potrzeby rozwiązywania problemów. Istniejącego wirtualnego dysku twardego należy dołączyć do tej maszyny Wirtualnej rozwiązywania problemów, aby przeglądać i edytowania zawartości na dysku. Ten proces umożliwia poprawić błędy konfiguracji lub przejrzyj dodatkowe aplikacji lub plików dziennika systemu, np. Wybierz lub Utwórz inną maszynę Wirtualną do użycia na potrzeby rozwiązywania problemów.

Po dołączeniu istniejącego wirtualnego dysku twardego, podaj adres URL do dysku uzyskanych w poprzednim `Get-AzureRmVM` polecenia. Poniższy przykład dołącza istniejącego wirtualnego dysku twardego do rozwiązywania problemów z maszyny Wirtualnej o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> Dodawanie dysku wymaga określenia rozmiaru dysku. Jak możemy dołączanie istniejącego dysku, `-DiskSizeInGB` jest określony jako `$null`. Ta wartość zapewnia dysk danych jest prawidłowo podłączony i bez konieczności należy określić wartość true, rozmiar dysku danych.


## <a name="mount-the-attached-data-disk"></a>Zainstalować dysk dołączonych danych

1. RDP do rozwiązywania problemów z maszyny Wirtualnej przy użyciu odpowiednich poświadczeń. Poniższy przykład pobierania pliku połączenia RDP dla maszyny Wirtualnej o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`i pobiera go do `C:\Users\ops\Documents`"

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Dysk danych jest automatycznie wykrywane i dołączony. Wyświetl listę dołączone woluminy w celu określenia litery dysku w następujący sposób:

    ```powershell
    Get-Disk
    ```

    Następujące przykładowe dane wyjściowe zawiera wirtualny dysk twardy podłączony dysk **2**. (Można również użyć `Get-Volume` Aby wyświetlić literę dysku):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalny wirtualny dysk twardy
Istniejącego wirtualnego dysku twardego zainstalowane można teraz wykonywać żadnych konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstaluj obraz i odłączyć oryginalny wirtualny dysk twardy
Po rozwiązaniu błędy, odinstaluj i odłączyć istniejącego wirtualnego dysku twardego z rozwiązywania problemów z maszyny Wirtualnej. Nie można użyć wirtualnego dysku twardego z innych maszyn wirtualnych, do czasu zwolnienia dzierżawy dołączanie wirtualnego dysku twardego do rozwiązywania problemów z maszyny Wirtualnej.

1. Z w ramach sesji protokołu RDP, Usuń dysk z danymi na maszyny Wirtualnej odzyskiwania. Należy numer dysku z poprzedniej `Get-Disk` polecenia cmdlet. Następnie należy użyć `Set-Disk` można ustawić dysku w trybie offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Upewnij się, dysk zostaje ustawione jako w trybie offline przy użyciu `Get-Disk` ponownie. Następujące przykładowe dane wyjściowe zawiera teraz dysk jest przełączany do trybu offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Zakończyć sesję RDP. W sesji programu Azure PowerShell należy usunąć wirtualnego dysku twardego z rozwiązywania problemów z maszyny Wirtualnej.

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej z oryginalny dysk twardy
Aby utworzyć Maszynę wirtualną z oryginalny wirtualny dysk twardy, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Rzeczywiste szablonu JSON jest przy użyciu następującego łącza:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

Szablon wdraża maszynę Wirtualną w istniejącej sieci wirtualnej przy użyciu adresu URL wirtualnego dysku twardego starszych polecenia. Poniższy przykład wdraża szablonu w grupie zasobów o nazwie `myResourceGroup`:

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

Odpowiedz monity dla szablonu, takie jak nazwa maszyny Wirtualnej, typ systemu operacyjnego i rozmiar maszyny Wirtualnej. `osDiskVhdUri` Jest taka sama jak wcześniej używane podczas dołączanie istniejącego wirtualnego dysku twardego do rozwiązywania problemów z maszyny Wirtualnej.


## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu

Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego diagnostyki rozruchu może nie automatycznie włączone. Poniższy przykład umożliwia włączenie diagnostycznych rozszerzenia na Maszynie wirtualnej o nazwie `myVMDeployed` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną, zobacz [połączeniami RDP Rozwiązywanie problemów z maszyną wirtualną Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby uzyskać więcej informacji dotyczących korzystania z Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
