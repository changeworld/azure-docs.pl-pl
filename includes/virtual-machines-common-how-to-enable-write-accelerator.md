---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 02/22/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 72d9ec52732a78e39f6481e2cb2d40f17f86f028
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478160"
---
# <a name="enable-write-accelerator"></a>Włączyć akcelerator zapisu

Akcelerator jest możliwość dysku dla serii M maszyn wirtualnych (VM w magazynie Premium Storage z usługą Azure Managed Disks) wyłącznie zapisu. Jak nazwy stanów, funkcji ma na celu poprawę opóźnień We/Wy zapisu względem usługi Azure Premium Storage. Zapisu akceleratora idealnie nadaje się gdzie aktualizacje plików dziennika są wymagane do utrwalenia na dysku w sposób wydajny wysoce nowoczesnej baz danych programu.

Zapisu akcelerator jest ogólnie dostępna w przypadku maszyn wirtualnych serii M w chmurze publicznej.

## <a name="planning-for-using-write-accelerator"></a>Planowanie zastosowania akcelerator zapisu

Zapisu akceleratora powinna być używana dla woluminów, które zawierają dziennika transakcji, lub wykonaj ponownie dzienniki DBMS. Nie zaleca się do użytku akceleratorem zapisu woluminach danych DBMS, ponieważ ta funkcja została zoptymalizowana do służyć do atakowania dyski dziennika.

Zapis akceleratora działa tylko w połączeniu z [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Włączanie akceleratorem zapisu na dysku systemu operacyjnego maszyny wirtualnej nastąpi ponowny rozruch maszyny Wirtualnej.
>
> Aby włączyć akcelerator zapisu do istniejącego dysku platformy Azure, który nie jest częścią tworzenia woluminu, poza wiele dysków o Windows menedżerów dysku lub woluminie, magazynowania w systemie Windows, Windows skalowalnego w poziomie serwer plików (SOFS) LVM systemu Linux lub MDADM, obciążenia, uzyskiwanie dostępu do dysku platformy Azure musi być wyłączony. Aplikacje baz danych przy użyciu dysku platformy Azure musi być zamknięty.
>
> Jeśli chcesz włączyć lub wyłączyć akceleratorem zapisu dla istniejącego woluminu, który został utworzony na podstawie wielu dysków Azure Premium Storage i rozłożone, używając menedżerów Windows dysk lub wolumin do magazynowania systemu Windows, Windows skalowalnego w poziomie pliku server (SOFS) LVM systemu Linux lub MDADM, wszystkie dyski wolumin budowanie musi włączać lub wyłączać dla akceleratorem zapisu w oddzielne kroki. **Przed przystąpieniem do włączania lub wyłączania akceleratorem zapisu w takiej konfiguracji, zamknij maszynę Wirtualną Azure**.

Włączanie akceleratorem zapisu dla dysków systemu operacyjnego nie powinien być niezbędne do konfiguracji SAP związane z maszyny Wirtualnej.

### <a name="restrictions-when-using-write-accelerator"></a>Ograniczenia w przypadku korzystania z akceleratorem zapisu

Korzystając z akceleratorem zapisu na dysku platformy Azure/VHD, są stosowane następujące ograniczenia:

- Buforowanie dysku Premium musi być równa "None" lub "Tylko do odczytu". Wszystkie tryby buforowania nie są obsługiwane.
- Migawka nie są obecnie obsługiwane dla dysków z włączonym akceleratorem zapisu. Podczas tworzenia kopii zapasowej usługa Azure Backup automatycznie wyłącza z włączonym akceleratorem zapisu dysków dołączonych do maszyny Wirtualnej.
- Tylko mniejsze rozmiary operacji We/Wy (< = 512 KiB) trwa jej jako przyspieszonej ścieżki. W obciążeniu sytuacji, w którym danych otrzymuje zbiorczego załadowane lub w przypadku, gdy buforów dziennika transakcji w różnych systemów DBMS są wypełnione w większym stopniu przed wprowadzenie utrwalone w magazynie, jest szansa, że zapisane we/wy dysku nie trwa jej jako przyspieszonej ścieżki.

Istnieją limity usługi Azure Premium Storage wirtualne dyski twarde dla maszyny Wirtualnej, które mogą być obsługiwane przez akcelerator zapisu. Bieżące limity są:

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Liczba dysków akcelerator zapisu | Zapis akceleratora dysku na SEKUNDĘ na maszynę Wirtualną |
| --- | --- | --- |
| M128ms, 128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10 000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Obowiązują limity operacji We/Wy na maszynie Wirtualnej i *nie* na dysk. Wszystkie dyski akceleratorem zapisu współużytkować ten sam limit operacji We/Wy dla maszyny Wirtualnej.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Włączanie akceleratorem zapisu na określonym dysku

Kilka następnych sekcji opisano, jak można się z włączonym akceleratorem zapisu na wirtualnych dyskach twardych programu Azure Premium Storage.

### <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne dotyczą użytkowania akceleratorem zapisu w tym momencie:

- Dyski, o których chcesz zastosować akcelerator zapisu platformy Azure, przed muszą być [usługi Azure managed disks](https://azure.microsoft.com/services/managed-disks/) w magazynie Premium Storage.
- Konieczne jest korzystanie z maszyn wirtualnych serii M

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Włączanie akcelerator zapisu platformy Azure przy użyciu programu Azure PowerShell

Moduł usługi Azure Power Shell w wersji 5.5.0 obejmują zmiany do odpowiednich poleceń cmdlet, aby włączyć lub wyłączyć akceleratorem zapisu dla określonych dysków Azure Premium Storage.
Aby włączyć lub wdrażanie dysków obsługiwanych przez akcelerator zapisu, następujące polecenia powłoki Power Shell stało się zmienić, a rozszerzony do akceptowania parametr dla akceleratorem zapisu.

Nowy parametr przełącznika **- WriteAccelerator** została dodana do następujących poleceń cmdlet:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Zapewniając nie parametrze ustawia właściwość na wartość false, a następnie wdroży dyski, na których nie są obsługiwane przez akcelerator zapisu.

Nowy parametr przełącznika **- OsDiskWriteAccelerator** została dodana do następujących poleceń cmdlet:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Nie został podany parametr ustawia właściwość na wartość false domyślnie zwracanie dysków, które nie korzystają z akceleratorem zapisu.

Nowe opcjonalny parametr typu Boolean (innych niż null), **- OsDiskWriteAccelerator** została dodana do następujących poleceń cmdlet:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Określ $true lub $false do kontrolowania obsługę akcelerator zapisu platformy Azure z dyskami.

Przykłady poleceń może wyglądać następująco:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dwa główne scenariusze mogą być uwzględnione w skryptach, jak pokazano w poniższych sekcjach.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Dodawanie nowego dysku obsługiwane przez akcelerator zapisu przy użyciu programu PowerShell

Ten skrypt można użyć, aby dodać nowy dysk do maszyny Wirtualnej. Dysków utworzonych za pomocą tego skryptu korzysta z akceleratorem zapisu.

Zastąp `myVM`, `myWAVMs`, `log001`, rozmiar dysku i LunID dysku przy użyciu wartości odpowiednich dla określonego wdrożenia.

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

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Włączanie akceleratorem zapisu na istniejącym dysku platformy Azure przy użyciu programu PowerShell

Aby włączyć akcelerator zapisu w istniejącego dysku, można użyć tego skryptu. Zastąp `myVM`, `myWAVMs`, i `test-log001` przy użyciu wartości odpowiednich dla określonego wdrożenia. Skrypt ten dodaje akceleratorem zapisu do istniejącego dysku gdzie wartość **$newstatus** jest ustawiona na "$true". Przy użyciu wartości "$false" spowoduje wyłączenie akceleratorem zapisu na danym dysku.

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
> Wykonywanie skryptu powyżej zostanie odłączenia określony dysk, włączyć akcelerator zapisu na dysku i ponownie podłączyć dysk

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Włączanie akceleratorem zapisu przy użyciu witryny Azure portal

Należy włączyć akcelerator zapisu za pośrednictwem portalu, w której możesz określić ustawienia buforowania dysku:

![Akcelerator zapisu w witrynie Azure portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Włączanie akceleratorem zapisu przy użyciu wiersza polecenia platformy Azure

Możesz użyć [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) włączyć akcelerator zapisu.

Aby włączyć akcelerator zapisu dla istniejącego dysku, należy użyć [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), może użyć poniższych przykładów, jeżeli wymienisz diskName VMName i grupie zasobów przy użyciu własnych wartości: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Można dołączyć dysku z akceleratorem zapisu włączone użycie [dołączanie dysku maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), może być skorzystaj z następującego przykładu, jeśli zastosować podstawienie w własnymi wartościami: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Aby wyłączyć akceleratorem zapisu, należy użyć [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), ustawienie wartości false dla właściwości: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Włączanie akceleratorem zapisu przy użyciu interfejsów API Rest

Aby wdrożyć za pomocą interfejsu API Rest platformy Azure, musisz zainstalować armclient platformy Azure.

### <a name="install-armclient"></a>Zainstaluj armclient

Aby uruchomić armclient, należy go zainstalować za pomocą narzędzia Chocolatey. Należy ją zainstalować, za pośrednictwem cmd.exe lub programu powershell. Podwyższonym poziomem uprawnień na użytek tych poleceń ("Uruchom jako Administrator").

Przy użyciu cmd.exe, uruchom następujące polecenie: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Korzystanie z powłoki Power Shell, uruchom następujące polecenie: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Teraz można zainstalować armclient przy użyciu następujące polecenie w program cmd.exe lub programu PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Pobieranie bieżącej konfiguracji maszyny Wirtualnej

Aby zmienić atrybuty konfiguracji dysku, należy najpierw pobrać bieżącą konfigurację w pliku JSON. Bieżącą konfigurację można uzyskać, wykonując następujące polecenie: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Zastąp warunkami w ramach <> <>dane, w tym nazwę pliku, które powinny mieć plik JSON.

Może wyglądać dane wyjściowe:

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

Następnie zaktualizuj plik JSON i włączyć akcelerator zapisu na dysku o nazwie "log1". Można to osiągnąć przez dodanie tego atrybutu w pliku JSON po wpisu pamięci podręcznej dysku.

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

Następnie zaktualizuj istniejące wdrożenie za pomocą następującego polecenia: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Wynik powinien wyglądać podobnie do poniższego. Aby zobaczyć, że jeden dysk z włączonym akceleratorem zapisu.

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

Po dokonaniu tej zmiany dysku powinien być obsługiwane przez akcelerator zapisu.
