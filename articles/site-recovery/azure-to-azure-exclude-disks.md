---
title: Wykluczanie dysków maszyn wirtualnych platformy Azure z replikacji za pomocą usługi Azure Site Recovery i programu Azure PowerShell
description: Dowiedz się, jak wykluczyć dyski maszyn wirtualnych platformy Azure podczas korzystania z usługi Azure Site Recovery przy użyciu programu Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973672"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Wykluczanie dysków z replikacji maszyn wirtualnych platformy Azure w programie PowerShell

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych platformy Azure. Można wykluczyć dyski w celu optymalizacji przepustowości używanej replikacji lub zasobów po stronie docelowej używanych przez te dyski. Obecnie ta funkcja jest dostępna tylko za pośrednictwem programu Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Upewnij się, że rozumiesz [architekturę i składniki odzyskiwania po awarii](azure-to-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](azure-to-azure-support-matrix.md) wszystkich składników.
- Upewnij się, że masz moduł AzureRm PowerShell "Az". Aby zainstalować lub zaktualizować program PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Upewnij się, że utworzono magazyn usług odzyskiwania i chronione maszyny wirtualne co najmniej raz. Jeśli nie wykonasz tych czynności, postępuj zgodnie z tym procesem w [witrynie Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell.](azure-to-azure-powershell.md)
- Jeśli szukasz informacji na temat dodawania dysków do maszyny Wirtualnej platformy Azure włączonej do replikacji, [zapoznaj się z tym artykułem](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Dlaczego warto wykluczyć dyski z replikacji
Może być konieczne wykluczenie dysków z replikacji, ponieważ:

- Twoja maszyna wirtualna osiągnęła [limity usługi Azure Site Recovery w celu replikowania szybkości zmiany danych.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- Dane, które są ubijane na wykluczonym dysku nie jest ważne lub nie muszą być replikowane.

- Chcesz zapisać zasoby magazynu i sieci, nie replikując danych.

## <a name="how-to-exclude-disks-from-replication"></a>Jak wykluczyć dyski z replikacji

W naszym przykładzie replikujemy maszynę wirtualną, która ma jeden system operacyjny i trzy dyski danych, który jest w regionie Wschodnie stany USA do zachodniego regionu US 2. Nazwa maszyny wirtualnej to *AzureDemoVM*. Wykluczamy dysk 1 i przechowujemy dyski 2 i 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Uzyskaj szczegółowe informacje o maszynach wirtualnych do replikacji

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

Uzyskaj szczegółowe informacje o dyskach maszyny wirtualnej. Te informacje będą używane później po uruchomieniu replikacji maszyny Wirtualnej.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikowanie maszyny wirtualnej platformy Azure

W poniższym przykładzie zakładamy, że masz już konto magazynu pamięci podręcznej, zasady replikacji i mapowania. Jeśli nie masz tych rzeczy, postępuj zgodnie z procesem w [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell.](azure-to-azure-powershell.md)

Replikowanie maszyny wirtualnej platformy Azure z *dyskami zarządzanymi*.

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

Gdy operacja replikacji rozruchowej zakończy się pomyślnie, dane maszyny Wirtualnej są replikowane do regionu odzyskiwania.

Możesz przejść do witryny Azure portal i wyświetlić replikowane maszyny wirtualne w obszarze "elementy replikowane".

Proces replikacji rozpoczyna się od wysiewu kopii dysków replikujących maszyny wirtualnej w regionie odzyskiwania. Ta faza jest nazywana fazą replikacji początkowej.

Po zakończeniu replikacji początkowej replikacja przechodzi do fazy synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona. Wybierz chroniona maszyna wirtualna, aby sprawdzić, czy dyski są wykluczone.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [uruchamianiu testu w pracy awaryjnej](site-recovery-test-failover-to-azure.md).
