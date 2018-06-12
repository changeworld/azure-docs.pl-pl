---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 6/8/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 21681a1af64754ef569f2ad4ff92f85a598007ac
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35323786"
---
# <a name="write-accelerator"></a>Zapis klawiszy skrótów
Zapisu akceleratora jest wyłącznie możliwości dysku dla serii M maszynach wirtualnych (VM na magazyn Azure dysków zarządzanych w warstwie Premium). Jako nazwa stany, cel funkcji jest ulepszenie opóźnień operacji We/Wy zapisu w usłudze Azure Premium Storage. Zapisu akceleratora doskonale nadaje gdzie aktualizacji plików dziennika są wymagane do utrwalenia na dysku w dużej wydajności sposób dla nowoczesnych baz danych.

Zapisu akceleratora jest ogólnie dostępna dla maszyn wirtualnych M serii w chmurze publicznej.

## <a name="planning-for-using-write-accelerator"></a>Planowanie za pomocą zapisu klawiszy skrótów
Zapisu akceleratora powinna być używana do woluminów, które zawierają dziennika transakcji lub wykonaj ponownie dzienniki systemu DBMS. Nie zaleca się na użytek zapisu akceleratora woluminach danych DBMS jako funkcja została zoptymalizowana do można użyć w odniesieniu do dysków dziennika.

Zapis akceleratora działa tylko w połączeniu z [dyskach zarządzanych Azure](https://azure.microsoft.com/services/managed-disks/). 


> [!IMPORTANT]
> Jeśli chcesz włączyć lub wyłączyć zapisu akceleratora dla istniejącego woluminu, który składa się z wielu dysków Azure Premium Storage i rozłożone przy użyciu menedżerów dysk lub wolumin systemu Windows, do magazynowania systemu Windows, Windows skalowalnego w poziomie pliku server (SOFS) Linux LVM lub MDADM, wszystkie Tworzenie woluminu dysków musi można włączać lub wyłączać dla zapisu akceleratora w oddzielne kroki. **Przed Włączanie lub wyłączanie akceleratora zapisu w przypadku takiej konfiguracji, zamykania maszyny Wirtualnej Azure**. 


> [!IMPORTANT]
> Aby umożliwić zapis klawiszy skrótów do istniejącego dysku platformy Azure, która nie jest częścią kompilacji woluminu poza wiele dysków z menedżerów dysk lub wolumin systemu Windows, Windows miejsca do magazynowania serwera plików systemu Windows skalowalnego w poziomie (SOFS) Linux LVM lub MDADM, obciążenie podczas uzyskiwania dostępu do dysku platformy Azure musi być wyłączony. Aplikacje baz danych przy użyciu dysku platformy Azure musi zostać zamknięta.

> [!IMPORTANT]
> Włączanie zapisu akceleratora dla dysku systemu operacyjnego maszyny wirtualnej spowoduje ponowny rozruch maszyny Wirtualnej. 

Włączanie akceleratora zapisu dla dysków systemu operacyjnego, nie powinno być konieczne w przypadku konfiguracji związanych z SAP maszyny Wirtualnej

### <a name="restrictions-when-using-write-accelerator"></a>Ograniczenia, używając zapisu klawiszy skrótów
Używając skrótów zapisu dla dysków Azure/VHD, zastosuj następujące ograniczenia:

- Buforowanie dysku Premium musi mieć wartość "None" lub "Tylko do odczytu". Wszystkie tryby buforowania nie są obsługiwane.
- Migawki na dysku zapisu włączone klawiszy skrótów nie jest jeszcze obsługiwany. To ograniczenie blokuje możliwość usługi Kopia zapasowa Azure wykonywania spójna migawka aplikacji wszystkich dysków maszyny wirtualnej.
- Tylko mniejszych we/wy (< = 32 KiB) trwa przyspieszonego ścieżki. Obciążenia pracą sytuacji, w którym danych otrzymuje zbiorczego załadowany lub w przypadku gdy buforów dziennika transakcji z innego systemu DBMS są wypełnione w większym stopniu przed pobierania utrwalone w magazynie, istnieje prawdopodobieństwo są zapisywane w operacji We/Wy dysku nie podejmuje przyspieszonego ścieżki.

Istnieją ograniczenia Azure Premium magazynu wirtualnych dysków twardych na maszynie Wirtualnej, która może być obsługiwana przez zapisu akceleratora. Bieżące ograniczenia są:

| JEDNOSTKA SKU MASZYNY WIRTUALNEJ | Liczba dysków zapisu klawiszy skrótów | Zapisu dysku akceleratora IOPS dla maszyny Wirtualnej |
| --- | --- | --- |
| M128ms, 128s | 16 | 8000 |
| M64s M64ms, M64ls, | 8 | 4000 |
| M32ms, M32s M32ls, M32ts, | 4 | 2000 | 
| M16ms, M16s | 2 | 1000 | 
| M8ms, M8s | 1 | 500 | 

Limity liczby są dla maszyny Wirtualnej i *nie* na dysku. Wszystkie dyski zapisu akceleratora współużytkować ten sam limit IOPS dla maszyny Wirtualnej.
## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Włączanie akceleratora zapisu na konkretnym dysku
Kilka kolejnych sekcjach opisano, jak napisać akceleratora można włączyć na wirtualnych dyskach twardych Azure Premium magazynu.


### <a name="prerequisites"></a>Wymagania wstępne
Następujące wymagania wstępne dotyczą użycie akceleratora zapisu w tym momencie:

- Dyski, które chcesz zastosować Azure akceleratora do zapisu względem muszą być [dyskach zarządzanych Azure](https://azure.microsoft.com/services/managed-disks/) na magazyn w warstwie Premium.
- Należy korzystać z serii M maszyny Wirtualnej

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Włączanie akceleratora zapisu Azure przy użyciu programu Azure PowerShell
Modułu powłoki Power Shell Azure z wersji 5.5.0 obejmują zmiany do odpowiednich poleceń cmdlet, aby włączyć lub wyłączyć zapisu akceleratora dla określonych dysków Azure Premium Storage.
Aby włączyć lub wdrożyć dysków obsługiwanych przez akceleratora zapisu, następujące polecenia powłoki Power Shell otrzymano zmiany i rozszerzony do akceptuje parametru do zapisania akceleratora.

Nowy parametr przełącznika "WriteAccelerator" zostały dodane do następujących poleceń cmdlet: 

- [Zestaw AzureRmVMOsDisk](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-6.0.0)
- [Add-AzureRmVMDataDisk](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Add-AzureRmVMDataDisk?view=azurermps-6.0.0)
- [Set-AzureRmVMDataDisk](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Set-AzureRmVMDataDisk?view=azurermps-6.0.0)
- [Add-AzureRmVmssDataDisk](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Add-AzureRmVmssDataDisk?view=azurermps-6.0.0)

Nie dają parametr ustawia tę właściwość na wartość false i wdroży dysków, które nie obsługują przez zapisu akceleratora.

Nowy parametr przełącznika "OsDiskWriteAccelerator" został dodany do następujących poleceń cmdlet: 

- [Set-AzureRmVmssStorageProfile](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile?view=azurermps-6.0.0)

Nie dają parametr ustawia tę właściwość na wartość false i będzie dostarczać dysków, które nie korzystać z akceleratora zapisu.

Nowy logiczna (z systemem innym niż null) parametr opcjonalny, "OsDiskWriteAccelerator" zostały dodane do następujących poleceń cmdlet: 

- [AzureRmVM aktualizacji](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Update-AzureRmVM?view=azurermps-6.0.0)
- [Update-AzureRmVmss](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Compute/Update-AzureRmVmss?view=azurermps-6.0.0)

Określ $true lub $false do kontrolowania Obsługa akceleratora zapisu Azure z dysków.

Przykłady poleceń może wyglądać jak:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Dwa główne scenariusze umożliwia pisanie skryptów, jak pokazano w poniższych sekcjach.

#### <a name="adding-a-new-disk-supported-by-write-accelerator"></a>Dodawanie nowego dysku obsługiwane przez zapisu klawiszy skrótów
Ten skrypt służy do dodawania nowego dysku do maszyny Wirtualnej. Utworzony za pomocą tego skryptu dysk będzie służyć zapisu akceleratora.

```

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
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
Należy dostosować nazwy maszyny Wirtualnej, dysk, grupy zasobów, rozmiar dysku i LunID dysku dla określonego wdrożenia.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>Włączenie akceleratora zapisu Azure istniejącego dysku platformy Azure
Jeśli musisz włączyć zapisu akceleratora istniejącego dysku służy tego skryptu do wykonania zadania:

```

#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Należy dostosować nazwy maszyny Wirtualnej, dysku i grupy zasobów. Skrypt powyżej dodaje zapisu klawiszy skrótów do istniejącego dysku, którego wartość $newstatus ustawiono "$true". Przy użyciu wartości "$false" spowoduje wyłączenie akceleratora zapisu na danym dysku.

> [!Note]
> Wykonywanie skryptu powyżej będzie odłączyć określonego dysku, Włącz zapisu akceleratora dla dysku i ponownie podłączyć dysk

### <a name="enabling-azure-write-accelerator-using-the-azure-portal"></a>Włączanie akceleratora zapisu Azure przy użyciu portalu Azure

Zapis akceleratora można włączyć w portalu, w którym określić ustawienia pamięci podręcznej dysku: 

![Zapis akceleratora w portalu Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-through-azure-cli"></a>Włączanie za pośrednictwem interfejsu wiersza polecenia platformy Azure
Można użyć [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) aby umożliwić zapis akceleratora. 

Akceleratora zapisu, Włącz istniejącego dysku, użyj [aktualizację maszyny wirtualnej az](https://docs.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest#az-vm-update), można używać poniższych przykładach, jeśli zastępuje diskName VMName i ResourceGroup własne:
 
```
az vm update -g group1 -n vm1 -write-accelerator 1=true
```
Na podłączenie dysku z akceleratora zapisu należy włączyć użyj [dołączyć dysku maszyny wirtualnej az](https://docs.microsoft.com/en-us/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), może skorzystaj z następującego przykładu, jeśli można użyć własnych wartości:
```
az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator
```
Aby wyłączyć akceleratora zapisu, należy użyć [aktualizację maszyny wirtualnej az](https://docs.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest#az-vm-update), ustawienie wartości false dla właściwości: 
```
az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false
```

### <a name="enabling-through-rest-apis"></a>Włączanie za pośrednictwem interfejsów API Rest
Aby można było wdrożyć za pomocą interfejsu API Rest Azure, musisz zainstalować Azure armclient

#### <a name="install-armclient"></a>Zainstaluj armclient

Aby uruchomić armclient, należy go zainstalować za pomocą Chocolatey. Można ją zainstalować za pomocą cmd.exe lub programu powershell. Użyj podwyższonym poziomem uprawnień dla tych poleceń ("Uruchom jako Administrator").

Przy użyciu cmd.exe uruchom następujące polecenie:

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

Przy użyciu powłoki Power Shell należy użyć:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Teraz można zainstalować armclient przy użyciu polecenia poniżej cmd.exe lub powłoki Power Shell

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>Pobieranie bieżącej konfiguracji maszyny Wirtualnej
Aby zmienić atrybuty konfiguracji dysku, należy najpierw pobrać bieżącą konfigurację w pliku JSON. Możesz uzyskać bieżącą konfigurację, wykonując następujące polecenie:

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
Zastąp warunków w ramach <> <>danych, łącznie z nazwą pliku, które powinny mieć pliku JSON.

Dane wyjściowe może wyglądać jak:

```
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

Następnym krokiem jest, aby zaktualizować plik JSON i włączyć akceleratora zapisu na dysku o nazwie "log1". Ten krok można osiągnąć przez dodanie tego atrybutu w pliku JSON po wpisu pamięci podręcznej dysku. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Następnie zaktualizuj istniejące wdrożenie za pomocą tego polecenia:

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

Dane wyjściowe powinny wyglądać tak jak poniżej. Widać, czy jest zapisu akceleratora włączone dla jednego dysku.

```
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
          **"writeAcceleratorEnabled": true,**
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

Z punktu zmiany na dysku powinny być obsługiwane zapisu akceleratora.

 
