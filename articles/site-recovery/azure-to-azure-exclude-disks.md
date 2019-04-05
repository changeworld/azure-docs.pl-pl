---
title: Usługa Azure Site Recovery - wykluczyć dysk, podczas replikacji maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykluczać dysku dla maszyn wirtualnych platformy Azure za pomocą usługi Azure Site Recovery przy użyciu programu Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 1c278d810df7e5ba8701529a59987c9bb16fa40c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044128"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Wykluczanie dysków z replikacji maszyn wirtualnych platformy Azure do platformy Azure przy użyciu programu Azure PowerShell

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych platformy Azure. Takie wykluczenie może zoptymalizować przepustowość używaną przez replikację lub zoptymalizować zasoby po stronie docelowej, z których korzystają takie dyski. Obecnie ta funkcja jest udostępniany wyłącznie za pośrednictwem programu Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](azure-to-azure-support-matrix.md) wszystkich składników.
- Masz programu Azure PowerShell `Az` modułu. Jeśli potrzebujesz zainstalować lub uaktualnić programu Azure PowerShell, postępuj zgodnie z tym [Przewodnik instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/install-az-ps).
- Utworzono już magazynu usługi Recovery services i zostały wykonane ochrony maszyn wirtualnych w co najmniej jeden raz. Jeśli nie wykonaj go, korzystając z dokumentacji, o których wspomniano [tutaj](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Dlaczego wykluczać dyski z replikacji?
Wykluczenie dysków z replikacji jest często konieczne, ponieważ:

- Maszyna wirtualna została osiągnięta [częstotliwość zmiany limity usługi Azure Site Recovery do replikacji danych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- Dane modyfikowane na wykluczonym dysku nie są istotne lub nie ma konieczności ich replikacji.

- Chcesz oszczędzić zasoby magazynu i zasoby sieciowe, pomijając replikowanie tych zmian.


## <a name="how-to-exclude-disks-from-replication"></a>Jak wykluczać dyski z replikacji?

W przykładzie w tym artykule maszynę wirtualną, która ma 1 systemu operacyjnego i dysków z danymi 3 w regionie wschodnie stany USA, będą replikowane do regionu zachodnie stany USA 2. Nazwa maszyny wirtualnej użytych w tym przykładzie jest AzureDemoVM. abyśmy spowoduje wykluczenie dysku 1 i zostanie zachowana na dysku, 2 i 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Uzyskaj szczegółowe informacje o maszyny wirtualne do replikacji

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```


Pobieranie szczegółów dysku dla dysków maszyny wirtualnej. Szczegóły dysku będzie używana później podczas uruchamiania replikacji dla maszyny wirtualnej.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Replikowanie maszyn wirtualnych platformy Azure

W poniższym przykładzie firma Microsoft ma zakłada, że masz już konto magazynu pamięci podręcznej, zasady replikacji i mapowania. Jeśli nie wykonaj go, korzystając z dokumentacji, o których wspomniano [tutaj](azure-to-azure-powershell.md) 


Replikowanie maszyn wirtualnych platformy Azure za pomocą **usługi managed disks**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Po rozpoczęcia replikacji powiedzie się, dane maszyny wirtualnej są replikowane w regionie odzyskiwania.

W obszarze zreplikowane elementy nie może uzyskać wprowadzenie replikowane maszyny wirtualne i możesz przejść do witryny Azure portal.
Proces replikacji rozpoczyna się przez wstępnie rozmieszczania kopię replikowanych dyskach maszyny wirtualnej w regionie odzyskiwania. Ta faza nosi nazwę fazy replikacji początkowej.

Po zakończeniu replikacji początkowej replikacji przenosi faza synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona. Kliknij na chronionej maszynie wirtualnej > dysków, aby zobaczyć, że jeśli dysk jest wyłączona lub nie.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowy tryb failover.
