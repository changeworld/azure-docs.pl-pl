---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74566326"
---
Akcelerator zapisu to funkcja dysków dla maszyn wirtualnych serii M (VM) w magazynie w jakości Premium wyłącznie z dyskami zarządzanymi platformy Azure. Jak sama nazwa wskazuje, celem funkcji jest zwiększenie opóźnienia we/wy zapisów w usłudze Azure Premium Storage. Akcelerator zapisu idealnie nadaje się tam, gdzie aktualizacje plików dziennika są wymagane do utrwalenia na dysku w sposób wysoce wydajny dla nowoczesnych baz danych.

Akcelerator zapisu jest ogólnie dostępny dla maszyn wirtualnych z serii M w chmurze publicznej.

## <a name="planning-for-using-write-accelerator"></a>Planowanie korzystania z akceleratora zapisu

Akcelerator zapisu powinien być używany dla woluminów, które zawierają dziennik transakcji lub ponownie dzienniki systemu dbms. Nie zaleca się używania akceleratora zapisu dla woluminów danych systemu dbms, ponieważ funkcja została zoptymalizowana do użycia na dyskach dziennika.

Akcelerator zapisu działa tylko w połączeniu z [dyskami zarządzanymi platformy Azure](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Włączenie akceleratora zapisu dla dysku systemu operacyjnego maszyny Wirtualnej spowoduje ponowne uruchomienie maszyny Wirtualnej.
>
> Aby włączyć akcelerator zapisu na istniejącym dysku platformy Azure, który NIE jest częścią kompilacji woluminu z wielu dysków z menedżerami dysków lub woluminów systemu Windows, miejsca do magazynowania systemu Windows, serwer plików w poziomie Windows skalowano w poziomie (SOFS), linux LVM lub MDADM, obciążenie uzyskujące dostęp do dysku platformy Azure należy wyłączyć. Aplikacje bazy danych przy użyciu dysku platformy Azure MUSI zostać zamknięty.
>
> Jeśli chcesz włączyć lub wyłączyć Akcelerator zapisu dla istniejącego woluminu, który jest zbudowany z wielu dysków usługi Azure Premium Storage i rozłożony przy użyciu menedżerów dysków lub woluminów systemu Windows, miejsca do magazynowania systemu Windows, serwer plików skalowano-w poziomie systemu Windows (SOFS), Linux LVM lub MDADM, wszystkie dysków budujących wolumin musi być włączony lub wyłączony dla akceleratora zapisu w oddzielnych krokach. **Przed włączeniem lub wyłączeniem akceleratora zapisu w takiej konfiguracji zamknij maszynę wirtualną platformy Azure**.

Włączenie akceleratora zapisu dla dysków systemu operacyjnego nie powinno być konieczne w przypadku konfiguracji maszyn wirtualnych związanych z sap.

### <a name="restrictions-when-using-write-accelerator"></a>Ograniczenia w przypadku korzystania z akceleratora zapisu

Podczas korzystania z akceleratora zapisu dla dysku/dysku VHD platformy Azure obowiązują następujące ograniczenia:

- Buforowanie dysku Premium musi być ustawione na "Brak" lub "Tylko do odczytu". Wszystkie inne tryby buforowania nie są obsługiwane.
- Migawka nie jest obecnie obsługiwana dla dysków obsługujących akcelerator zapisu. Podczas wykonywania kopii zapasowej usługa Azure Backup automatycznie wyklucza dyski z włączonym akceleratorem zapisu podłączone do maszyny Wirtualnej.
- Tylko mniejsze rozmiary we/wy (<= 512 KiB) przyjmują przyspieszoną ścieżkę. W sytuacjach obciążenia, w których dane są coraz zbiorczo załadowany lub gdzie bufory dziennika transakcji różnych dbms są wypełniane w większym stopniu przed uzyskaniem utrwalone do magazynu, są szanse, że we/wy zapisane na dysku nie przyjmuje przyspieszonej ścieżki.

Istnieją limity dysków wirtualnych usługi Azure Premium Storage na maszynę wirtualną, które mogą być obsługiwane przez akcelerator zapisu. Obecne limity to:

| Jednostka SKU maszyny wirtualnej | Liczba dysków akceleratora zapisu | IOPS dysku akceleratora zapisu na maszynę wirtualną |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10 000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10 000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Limity we/wy są na maszynę wirtualną, a *nie* na dysk. Wszystkie dyski akceleratora zapisu współużytkują ten sam limit we/wy na maszynę wirtualną.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Włączanie akceleratora zapisu na określonym dysku

W następnych kilku sekcjach opisano, jak można włączyć akcelerator zapisu na dyskach wirtualnych usługi Azure Premium Storage.

### <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne mają zastosowanie do użycia akceleratora zapisu w tym momencie:

- Dyski, które chcesz zastosować akceleratora zapisu azure wobec muszą być [dyski zarządzane platformy Azure](https://azure.microsoft.com/services/managed-disks/) w magazynie w jakości.
- Maszyna wirtualna z serii M musi być

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Włączanie akceleratora zapisu platformy Azure przy użyciu programu Azure PowerShell

Moduł power shell platformy Azure z wersji 5.5.0 zawiera zmiany odpowiednich poleceń cmdlet, aby włączyć lub wyłączyć akcelerator zapisu dla określonych dysków usługi Azure Premium Storage.
Aby włączyć lub wdrożyć dyski obsługiwane przez akcelerator zapisu, następujące polecenia power shell zostały zmienione i rozszerzone, aby zaakceptować parametr akceleratora zapisu.

Nowy parametr switch, **-WriteAccelerator** został dodany do następujących poleceń cmdlet:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Dodatek AzVMDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Identyfikator dodatkowych AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Nieudowonianie parametr ustawia właściwość na false i wdroży dyski, które nie mają obsługi przez akcelerator zapisu.

Nowy parametr switch, **-OsDiskWriteAccelerator** został dodany do następujących poleceń cmdlet:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Nieokreślenie parametru domyślnie ustawia właściwość na false, zwracając dyski, które nie wykorzystują akceleratora zapisu.

Nowy opcjonalny parametr logiczny (nienastępalny) **-OsDiskWriteAccelerator** został dodany do następujących poleceń cmdlet:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Określ $true lub $false, aby kontrolować obsługę akceleratora zapisu azure z dyskami.

Przykłady poleceń mogą wyglądać następująco:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dwa główne scenariusze mogą być skryptowane, jak pokazano w poniższych sekcjach.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Dodawanie nowego dysku obsługiwanego przez akcelerator zapisu przy użyciu programu PowerShell

Za pomocą tego skryptu można dodać nowy dysk do maszyny Wirtualnej. Dysk utworzony za pomocą tego skryptu używa akceleratora zapisu.

`myVM`Zamień `log001`, `myWAVMs`, , rozmiar dysku i LunID dysku z wartościami odpowiednimi dla danego wdrożenia.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Włączanie akceleratora zapisu na istniejącym dysku platformy Azure przy użyciu programu PowerShell

Za pomocą tego skryptu można włączyć akcelerator zapisu na istniejącym dysku. Zamień `myVM`i `myWAVMs` `test-log001` wartości odpowiednie dla danego wdrożenia. Skrypt dodaje Akcelerator zapisu do istniejącego dysku, na którym wartość **$newstatus** jest ustawiona na "$true". Użycie wartości "$false" spowoduje wyłączenie akceleratora zapisu na danym dysku.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Wykonanie skryptu powyżej spowoduje odłączenie określonego dysku, włączenie akceleratora zapisu względem dysku, a następnie ponowne dołączenie dysku

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Włączanie akceleratora zapisu platformy Azure przy użyciu witryny Azure Portal

Akcelerator zapisu można włączyć za pośrednictwem portalu, w którym określono ustawienia buforowania dysku:

![Akcelerator zapisu w witrynie Azure portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Włączanie akceleratora zapisu platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) można włączyć akcelerator zapisu.

Aby włączyć akcelerator zapisu na istniejącym dysku, użyj [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), można użyć następujących przykładów, jeśli zastąpisz diskName, VMName i ResourceGroup własnymi wartościami:`az vm update -g group1 -n vm1 -write-accelerator 1=true`

Aby dołączyć dysk z włączoną akceleratorem zapisu, użyj [dysku Az vm attach,](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach)można użyć następującego przykładu, jeśli zastąpisz własne wartości:`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Aby wyłączyć akcelerator zapisu, użyj [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), ustawiając właściwości na false:`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Włączanie akceleratora zapisu przy użyciu interfejsów API spoczynku

Aby wdrożyć za pośrednictwem interfejsu API usługi Azure Rest, należy zainstalować armclient platformy Azure.

### <a name="install-armclient"></a>Zainstaluj armclient

Aby uruchomić armclient, trzeba go zainstalować przez Chocolatey. Można go zainstalować za pośrednictwem cmd.exe lub powershell. Użyj praw z podwyższonym poziomem uprawnień dla tych poleceń ("Uruchom jako administrator").

Korzystając z programu cmd.exe, uruchom następujące polecenie:`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Korzystając z powłoki zasilania, uruchom następujące polecenie:`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Teraz można zainstalować armclient za pomocą następującego polecenia w cmd.exe lub PowerShell`choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Uzyskiwanie bieżącej konfiguracji maszyny Wirtualnej

Aby zmienić atrybuty konfiguracji dysku, należy najpierw uzyskać bieżącą konfigurację w pliku JSON. Bieżącą konfigurację można uzyskać, wykonując następujące polecenie:`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Zastąp terminy w "<<   >>" danymi, w tym nazwą pliku JSON.

Dane wyjściowe mogą wyglądać następująco:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Następnie zaktualizuj plik JSON i włącz akcelerator zapisu na dysku o nazwie "log1". Można to osiągnąć, dodając ten atrybut do pliku JSON po wprowadzeniu pamięci podręcznej dysku.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Następnie zaktualizuj istniejące wdrożenie za pomocą tego polecenia:`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Dane wyjściowe powinny wyglądać jak poniżej. Widać, że akcelerator zapisu włączony dla jednego dysku.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Po wprowadzeniu tej zmiany dysk powinien być obsługiwany przez akcelerator zapisu.
