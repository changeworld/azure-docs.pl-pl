---
title: Azure zapisu klawisz skrótu dla wdrożenia SAP | Dokumentacja firmy Microsoft
description: Przewodnik obsługi programu SAP HANA systemów, które zostały wdrożone na maszynach wirtualnych Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0cb9b4003faa2ccdd07ccc78c2095472690f0e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>Azure zapisu klawisz skrótu dla wdrożenia SAP
Azure akceleratora zapisu jest funkcje, które jest pierwsze wprowadzanie dla maszyn wirtualnych, M serii wyłącznie. Accelerator zapisu Azure nie jest dostępna z żadnych innych maszyn wirtualnych serii na platformie Azure, z wyjątkiem serii M. Jako nazwa stany, celem funkcji jest zwiększyć czas oczekiwania operacji We/Wy zapisu w usłudze Azure Premium Storage. 

>[!NOTE]
> W tym momencie akceleratora zapisu Azure znajduje się w publicznej wersji zapoznawczej i wymaga białe listy identyfikator subskrypcji platformy Azure

Funkcja Azure akceleratora zapisu jest dostępna dla wdrażania serii M publicznej wersji zapoznawczej w:

- US2 zachodnie
- Europa Zachodnia
- Azja Południowo-Wschodnia

## <a name="planning-for-using-azure-write-accelerator"></a>Planowanie zastosowania akceleratora zapisu Azure
Azure akceleratora zapisu powinien być używany dla woluminów, które zawierają dziennika transakcji lub wykonaj ponownie dzienniki systemu DBMS. Nie zaleca się użyć akceleratora zapisu Azure dla woluminów danych z bazami danych. Powód, aby to ograniczenie jest, że akceleratora zapisu Azure wymaga Azure Premium magazynu dysków VHD ma zostać zainstalowany bez dodatkowych buforowania odczytu dostępnej dla usługi Premium Storage. W przypadku tradycyjnych baz danych można zaobserwować większe korzyści z tym typem buforowania. Ponieważ zapisu akceleratora ma wpływ tylko na działania zapisu, a nie przyspiesza odczytów, obsługiwanych projektu dla SAP jest użyj zapisu akceleratora przed dziennika transakcji lub wykonaj ponownie dyski dziennika bazy danych SAP obsługiwane. 

Azure akceleratora zapisu działa tylko w połączeniu z [dyskach zarządzanych Azure](https://azure.microsoft.com/services/managed-disks/). W związku z tym należy odpowiednio zaplanować. 

>[!NOTE]
> Funkcje akceleratora zapisu Azure jest nadal w publicznej wersji zapoznawczej, nie scenariuszy wdrożenia produkcyjnego są obsługiwane w funkcji jeszcze.

Istnieją ograniczenia Azure Premium magazynu wirtualne dyski twarde dla maszyny Wirtualnej, która może być obsługiwana przez akceleratora zapisu Azure. Bieżące ograniczenia są:

- 16 dysków VHD dla M128xx maszyny Wirtualnej
- 8 dysków VHD dla M64xx maszyny Wirtualnej

> [!IMPORTANT]
> Jeśli chcesz włączyć lub wyłączyć Azure zapisu akceleratora dla istniejącego woluminu, który składa się z wielu dysków Azure Premium Storage i rozłożone przy użyciu menedżerów dysk lub wolumin systemu Windows, do magazynowania systemu Windows, Windows skalowalnego w poziomie pliku server (SOFS) Linux LVM lub MDADM, wszystkie dyski tworzenia woluminu musi być włączone lub wyłączone dla zapisu akceleratora w oddzielne kroki. **Przed Włączanie lub wyłączanie akceleratora zapisu w przypadku takiej konfiguracji, zamykania maszyny Wirtualnej Azure**. 


> [!IMPORTANT]
> Aby włączyć Azure zapisu akceleratora do istniejącego dysku Azure, który nie jest częścią kompilacji woluminu poza wiele dysków o dysk systemu Windows lub menedżerów wolumin miejsca do magazynowania systemu Windows, skalowalnych w poziomie systemu Windows serwera plików (SOFS), Linux LVM albo MDADM, jest obciążenie, uzyskiwanie dostępu Dysku platformy Azure musi być wyłączony. Aplikacje baz danych przy użyciu dysku platformy Azure musi zostać zamknięta.

> [!IMPORTANT]
> Włączanie zapisu akceleratora dla dysku systemu operacyjnego maszyny Wirtualnej Azure maszyny wirtualnej zostanie ponowny rozruch maszyny Wirtualnej. 

Włączanie akceleratora zapisu Azure działających dysków, nie powinna być niezbędne do programu SAP związane z konfiguracji maszyny Wirtualnej

### <a name="restrictions-when-using-azure-write-accelerator"></a>Ograniczenia, korzystając z akceleratora zapisu Azure
W przypadku używania Azure akceleratora zapisu dla dysków Azure/VHD, następujące ograniczenia:

- Buforowanie dysku Premium musi być ustawiona na "None" lub "Tylko do odczytu". Wszystkie tryby buforowania nie są obsługiwane.
- Migawki na dysku zapisu włączone klawiszy skrótów nie jest jeszcze obsługiwany. To ograniczenie blokuje możliwość usługi Kopia zapasowa Azure wykonywania spójna migawka aplikacji wszystkich dysków maszyny wirtualnej.
- Tylko mniejszych we/wy trwa przyspieszonego ścieżki. Obciążenia pracą sytuacji, w którym danych otrzymuje zbiorczego załadowany lub w przypadku gdy buforów dziennika transakcji z innego systemu DBMS są wypełnione w większym stopniu przed pobierania utrwalone w magazynie, istnieje prawdopodobieństwo są zapisywane w operacji We/Wy dysku nie podejmuje przyspieszonego ścieżki.


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Włączanie akceleratora zapisu na konkretnym dysku
Kilka kolejnych sekcjach opisano, jak akceleratora zapisu Azure można włączyć na wirtualnych dyskach twardych Azure Premium magazynu.

W tym momencie włączania zapisu akceleratora za pośrednictwem interfejsu API Rest Azure i powłoki Power Shell są tylko metody. Inne metody do pomocy technicznej na platformie Azure portal zastosują się w ciągu następnych kilku tygodni.

### <a name="prerequisites"></a>Wymagania wstępne
Następujące wymagania wstępne dotyczą użycie akceleratora zapisu Azure w tym momencie:

- Identyfikator subskrypcji, który został użyty do wdrożenia maszyny Wirtualnej i magazynu dla maszyny Wirtualnej musi być wymienione biały. Skontaktuj się z Microsoft CSA, GBB, lub wymienione biały Menedżera konta, aby uzyskać identyfikator subskrypcji. 
- Dyski, które chcesz zastosować Azure akceleratora do zapisu względem muszą być [dyskach zarządzanych Azure](https://azure.microsoft.com/services/managed-disks/).

### <a name="enabling-through-power-shell"></a>Włączanie za pomocą powłoki Power Shell
Modułu powłoki Power Shell Azure z wersji 5.5.0 obejmują zmiany do odpowiednich poleceń cmdlet, aby włączyć lub wyłączyć Azure zapisu akceleratora dla określonych dysków Azure Premium Storage.
Aby włączyć lub wdrożyć dysków obsługiwanych przez akceleratora zapisu, następujące polecenia powłoki Power Shell otrzymano zmiany i rozszerzony do akceptuje parametru do zapisania akceleratora.

Nowy parametr przełącznika "WriteAccelerator" zostały dodane do następujących poleceń cmdlet: 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

Nie dają parametr ustawia tę właściwość na wartość false i wdroży dysków, które nie obsługują przez Azure zapisu akceleratora.

Nowy parametr przełącznika "OsDiskWriteAccelerator" został dodany do następujących poleceń cmdlet: 

- Set-AzureRmVmssStorageProfile

Nie dając zestawów właściwości na wartość false i będzie dostarczać dysków, które nie korzystać z akceleratora zapisu Azure.

Nowy logiczna (z systemem innym niż null) parametr opcjonalny, "OsDiskWriteAccelerator" zostały dodane do następujących poleceń cmdlet: 

- Update-AzureRmVM
- Update-AzureRmVmss

Określ $true lub $false do kontrolowania Obsługa akceleratora zapisu Azure z dysków.

Przykłady poleceń może wyglądać jak:

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

Dwa główne scenariusze umożliwia pisanie skryptów, jak pokazano w poniższych sekcjach.

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>Dodawanie nowego dysku obsługiwane przez akceleratora zapisu Azure
Ten skrypt służy do dodawania nowego dysku do maszyny Wirtualnej. Dysk utworzony za pomocą tego skryptu zamierza użyć akceleratora zapisu Azure.

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
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
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

Należy dostosować nazwy maszyny Wirtualnej, dysku i grupy zasobów. Skrypt powyżej dodaje zapisu klawiszy skrótów do istniejącego dysku jest dla $newstatus ma wartość "$true". Przy użyciu wartości "$false" spowoduje wyłączenie akceleratora zapisu na danym dysku.

> [!Note]
> Wykonywanie skryptu powyżej będzie odłączyć określonego dysku, Włącz zapisu akceleratora dla dysku i ponownie podłączyć dysk




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

 