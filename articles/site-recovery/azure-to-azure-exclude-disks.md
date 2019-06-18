---
title: Usługa Azure Site Recovery - wykluczania dysków podczas replikacji maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykluczać dyski maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery przy użyciu programu Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160307"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Wykluczanie dysków z replikacji z programu PowerShell maszyn wirtualnych platformy Azure

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych platformy Azure. Może być wykluczyć dyski, aby zoptymalizować przepustowość używaną przez replikację lub zasobów po stronie docelowej, korzystających z tych dysków. Obecnie ta funkcja jest dostępna wyłącznie za pośrednictwem programu Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Upewnij się, że rozumiesz [składnikach i architekturze odzyskiwania po awarii](azure-to-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](azure-to-azure-support-matrix.md) wszystkich składników.
- Upewnij się, że masz AzureRm PowerShell "Az" modułu. Aby zainstalować lub zaktualizować program PowerShell, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Upewnij się, że utworzono magazyn usługi recovery services i chronione maszyny wirtualne w co najmniej raz. Jeśli nie zostało wykonane te rzeczy, wykonaj procedurę w [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Dlaczego wykluczać dyski z replikacji
Może być konieczne wykluczanie dysków z replikacji, ponieważ:

- Maszyna wirtualna została osiągnięta [częstotliwość zmiany limity usługi Azure Site Recovery do replikacji danych](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Dane modyfikowane na wykluczonym dysku nie są ważne lub nie mają być replikowane.

- Chcesz oszczędzić zasoby magazynu i sieci, nie będą replikowane dane.

## <a name="how-to-exclude-disks-from-replication"></a>Jak wykluczać dyski z replikacji

W tym przykładzie firma Microsoft replikować maszynę wirtualną, która ma jeden system operacyjny i trzech dysków z danymi, które w regionie wschodnie stany USA, w regionie zachodnie stany USA 2. Nazwa maszyny wirtualnej jest *AzureDemoVM*. Firma Microsoft wykluczyć dysk 1 i zachowanie dysków, 2 i 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Pobieranie szczegółów maszyny wirtualne do replikacji

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

Uzyskaj szczegółowe informacje o dyskach maszyny wirtualnej. Te informacje będą używane później, po uruchomieniu replikacji maszyny wirtualnej.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikowanie maszyn wirtualnych platformy Azure

W poniższym przykładzie przyjęto założenie, że masz już konto magazynu pamięci podręcznej, zasady replikacji i mapowania. Jeśli nie masz tych rzeczy, należy wykonać ten proces w [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell](azure-to-azure-powershell.md).

Replikowanie maszyn wirtualnych platformy Azure za pomocą *usługi managed disks*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

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


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Po pomyślnym zakończeniu operacji rozpoczęcia replikacji, dane maszyny Wirtualnej są replikowane w regionie odzyskiwania.

Możesz przejść do witryny Azure portal i wyświetlić replikowane maszyny wirtualne w obszarze "zreplikowane elementy".

Proces replikacji rozpoczyna się przez wstępne wypełnienie kopię replikowanych dyskach maszyny wirtualnej w regionie odzyskiwania. Ta faza nosi nazwę fazy replikacji początkowej.

Po zakończeniu replikacji początkowej replikacji przechodzi do fazy synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona. Wybierz chronionej maszyny wirtualnej, aby zobaczyć, jeśli wszystkie dyski są wyłączone.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Uruchamianie testu trybu failover](site-recovery-test-failover-to-azure.md).
