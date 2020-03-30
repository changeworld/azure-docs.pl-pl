---
title: Odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure PowerShell i usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure za pomocą usługi Azure Site Recovery przy użyciu programu Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 583511194fb100add1d5fc4ea9c06a869cf652b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212281"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell

W tym artykule zobaczysz, jak skonfigurować i przetestować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell.

Omawiane kwestie:

> [!div class="checklist"]
> - Utwórz magazyn usługi Recovery Services.
> - Ustawianie kontekstu przechowalni dla sesji programu PowerShell.
> - Przygotuj magazyn, aby rozpocząć replikowanie maszyn wirtualnych platformy Azure.
> - Tworzenie mapowań sieciowych.
> - Tworzenie kont magazynu do replikowania maszyn wirtualnych.
> - Replikuj maszyny wirtualne platformy Azure do regionu odzyskiwania w celu odzyskiwania po awarii.
> - Wykonaj test pracy awaryjnej, sprawdzania poprawności i oczyszczania testu pracy awaryjnej.
> - Przeładuj awaryjnie do regionu odzyskiwania.

> [!NOTE]
> Nie wszystkie możliwości scenariusza dostępne za pośrednictwem portalu mogą być dostępne za pośrednictwem programu Azure PowerShell. Niektóre z możliwości scenariusza, które nie są obecnie obsługiwane za pośrednictwem programu Azure PowerShell, są następujące:
> - Możliwość określenia, że wszystkie dyski na maszynie wirtualnej powinny być replikowane bez konieczności jawnego określania każdego dysku maszyny wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](azure-to-azure-support-matrix.md) wszystkich składników.
- Masz moduł programu Azure `Az` PowerShell. Jeśli chcesz zainstalować lub uaktualnić program Azure PowerShell, postępuj zgodnie z tym [przewodnikiem, aby zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Logowanie się do subskrypcji platformy Microsoft Azure

Zaloguj się do subskrypcji `Connect-AzAccount` platformy Azure za pomocą polecenia cmdlet.

```azurepowershell
Connect-AzAccount
```

Wybierz swoją subskrypcję platformy Azure. Użyj `Get-AzSubscription` polecenia cmdlet, aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp. Wybierz subskrypcję platformy Azure, `Set-AzContext` aby pracować przy użyciu polecenia cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Uzyskaj szczegółowe informacje o zreplikowanej maszynie wirtualnej

W tym artykule maszyna wirtualna w regionie Wschodnie stany USA jest replikowana do i odzyskiwana w regionie Zachodnie stany USA 2. Replikowana maszyna wirtualna ma dysk systemu operacyjnego i jeden dysk danych. Nazwa maszyny wirtualnej użytej w `AzureDemoVM`przykładzie jest .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

Uzyskaj szczegóły dysku dla dysków maszyny wirtualnej. Szczegóły dysku będą używane później podczas uruchamiania replikacji dla maszyny wirtualnej.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Utwórz grupę zasobów, w której ma być utworzony magazyn usług odzyskiwania.

> [!IMPORTANT]
> * Magazyn usług odzyskiwania i chronione maszyny wirtualne muszą znajdować się w różnych lokalizacjach platformy Azure.
> * Grupa zasobów magazynu usług odzyskiwania i chronionych maszyn wirtualnych muszą znajdować się w różnych lokalizacjach platformy Azure.
> * Magazyn usług odzyskiwania i grupa zasobów, do której należy, może znajdować się w tej samej lokalizacji platformy Azure.

W przykładzie w tym artykule chronionej maszyny wirtualnej znajduje się w regionie wschodnich stanów USA. Region odzyskiwania wybrany do odzyskiwania po awarii jest region zachodnia stany USA 2. Magazyn usług odzyskiwania i grupa zasobów magazynu znajdują się zarówno w regionie odzyskiwania, West US 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Tworzenie magazynu usług odzyskiwania. W tym przykładzie magazyn `a2aDemoRecoveryVault` usług odzyskiwania o nazwie jest tworzony w regionie Zachodnie stany USA 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>Ustawianie kontekstu przechowalni

Ustaw kontekst przechowalni do użycia w sesji programu PowerShell. Po ustawieniu kontekstu przechowalni operacje odzyskiwania witryny platformy Azure w sesji programu PowerShell są wykonywane w kontekście wybranego magazynu.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

W przypadku migracji między platformą Azure można ustawić kontekst przechowalni na nowo utworzony magazyn:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Przygotowanie magazynu do rozpoczęcia replikowania maszyn wirtualnych platformy Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Tworzenie obiektu sieci szkieletowej odzyskiwania witryny w celu reprezentowania regionu podstawowego (źródłowego)

Obiekt sieci szkieletowej w przechowalni reprezentuje region platformy Azure. Podstawowy obiekt sieci szkieletowej jest tworzony w celu reprezentowania regionu platformy Azure, do którego należą maszyny wirtualne chronione w przechowalni. W przykładzie w tym artykule chronionej maszyny wirtualnej znajduje się w regionie wschodnich stanów USA.

- Na jeden obiekt tkaniny można utworzyć tylko jeden obiekt tkaniny.
- Jeśli wcześniej włączono replikację usługi Site Recovery dla maszyny Wirtualnej w witrynie Azure portal, usługa Site Recovery automatycznie tworzy obiekt sieci szkieletowej. Jeśli obiekt tkaniny istnieje dla regionu, nie można utworzyć nowego.

Przed rozpoczęciem należy zrozumieć, że operacje odzyskiwania witryny są wykonywane asynchronicznie. Podczas inicjowania operacji jest przesyłane zadanie usługi Azure Site Recovery i zwracany jest obiekt śledzenia zadań. Użyj obiektu śledzenia zadań, aby uzyskać najnowszy`Get-AzRecoveryServicesAsrJob`stan zadania ( ) i monitorować stan operacji.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Jeśli maszyny wirtualne z wielu regionów platformy Azure są chronione w tym samym magazynie, utwórz jeden obiekt sieci szkieletowej dla każdego źródłowego regionu platformy Azure.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Tworzenie obiektu sieci szkieletowej odzyskiwania witryny w celu reprezentowania regionu odzyskiwania

Obiekt sieci szkieletowej odzyskiwania reprezentuje odzyskiwanie lokalizacji platformy Azure. Jeśli istnieje przewijania awaryjnego, maszyny wirtualne są replikowane i odzyskane do regionu odzyskiwania reprezentowanego przez sieć szkieletową odzyskiwania. Region platformy Azure odzyskiwania używany w tym przykładzie jest West US 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Tworzenie kontenera ochrony odzyskiwania witryny w głównej sieci szkieletowej

Kontener ochrony jest kontenerem używanym do grupowania zreplikowanych elementów w sieci szkieletowej.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Tworzenie kontenera ochrony odzyskiwania witryny w sieci szkieletowej odzyskiwania

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Tworzenie mapowania kontenera ochrony między kontenerem ochrony podstawowej i odzyskiwania

Mapowanie kontenera ochrony mapuje podstawowy kontener ochrony za pomocą kontenera ochrony odzyskiwania i zasad replikacji. Utwórz jedno mapowanie dla każdej zasady replikacji, która będzie używana do replikowania maszyn wirtualnych między parą kontenera ochrony.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Tworzenie mapowania kontenera ochrony dla powrotu po awarii (replikacja odwrotna po przebaczeniu awaryjnym)

Po przełączeniu w tryb failover, gdy jesteś gotowy do wprowadzenia maszyny wirtualnej po awarii z powrotem do oryginalnego regionu platformy Azure, należy wykonać powrót po awarii. Aby przywrócić po awarii, maszyna wirtualna po awarii jest replikowana odwrotnie z regionu po awarii do regionu oryginalnego. W przypadku replikacji odwrotnej role oryginalnego regionu i przełącznika regionu odzyskiwania. Region pierwotny staje się teraz nowym regionem odzyskiwania, a region odzyskiwania stał się regionem podstawowym. Mapowanie kontenera ochrony dla replikacji odwrotnej reprezentuje przełączane role regionów oryginalnego i odzyskiwania.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Tworzenie konta magazynu pamięci podręcznej i docelowego konta magazynu

Konto magazynu pamięci podręcznej jest standardowym kontem magazynu w tym samym regionie platformy Azure, co replikowana maszyna wirtualna. Konto magazynu pamięci podręcznej jest używane do tymczasowego przechowywania zmian replikacji, zanim zmiany zostaną przeniesione do regionu platformy Azure odzyskiwania. Można wybrać, ale nie jest konieczne, aby określić różne konta magazynu pamięci podręcznej dla różnych dysków maszyny wirtualnej.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

W przypadku maszyn wirtualnych, które **nie używają dysków zarządzanych, docelowe**konto magazynu jest kontem magazynu w regionie odzyskiwania, do którego są replikowane dyski maszyny wirtualnej. Docelowe konto magazynu może być standardowym kontem magazynu lub kontem magazynu w jakości Premium. Wybierz rodzaj konta magazynu wymagane na podstawie szybkości zmiany danych (szybkość zapisu we/wy) dla dysków i azure site recovery obsługiwane limity zmian dla typu magazynu.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Tworzenie mapowania sieci

Mapowanie sieci mapuje sieci wirtualne w regionie podstawowym do sieci wirtualnych w regionie odzyskiwania. Mapowanie sieci określa sieć wirtualną platformy Azure w regionie odzyskiwania, do których maszyna wirtualna w podstawowej sieci wirtualnej powinna zostać przejęta awaryjnie. Jedna sieć wirtualna platformy Azure może być mapowana tylko do jednej sieci wirtualnej platformy Azure w regionie odzyskiwania.

- Utwórz sieć wirtualną platformy Azure w regionie odzyskiwania, aby przejść w tryb fail over:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Pobierz podstawową sieć wirtualną. Sieć wirtualna, do którą jest podłączona maszyna wirtualna:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Tworzenie mapowania sieci między podstawową siecią wirtualną a siecią wirtualną odzyskiwania:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Tworzenie mapowania sieci dla kierunku odwrotnego (powrót po awarii):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replikowanie maszyny wirtualnej platformy Azure

Replikowanie maszyny wirtualnej platformy Azure za pomocą **dysków zarządzanych**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replikowanie maszyny wirtualnej platformy Azure za pomocą **dysków niezarządzanych**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Po pomyślnym zakończeniu operacji replikacji początkowej dane maszyny wirtualnej są replikowane do regionu odzyskiwania.

Proces replikacji rozpoczyna się od początkowego rozmieszczenia kopii dysków replikujących maszyny wirtualnej w regionie odzyskiwania. Ta faza jest nazywana początkową fazą replikacji.

Replikacja początkowa AFter kończy się, replikacja przechodzi do fazy synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona i można wykonać na niej operację trybu failover testu. Stan replikacji zreplikowanego elementu reprezentującego maszynę wirtualną przechodzi do stanu **chronionego** po zakończeniu replikacji początkowej.

Monitoruj stan replikacji i kondycję replikacji maszyny wirtualnej, uzyskując szczegółowe informacje o odpowiadającym jej elementowi chronionemu replikacją.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Wykonaj test pracy awaryjnej, sprawdzania poprawności i oczyszczania testu w pracy awaryjnej

Po zakończeniu replikacji maszyny wirtualnej w stanie chronionym można wykonać testową operację trybu failover na maszynie wirtualnej (na chronionym elementem replikacji maszyny wirtualnej).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Wykonaj test pracy awaryjnej.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Poczekaj na zakończenie operacji pracy awaryjnej testu.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Po pomyślnym zakończeniu zadania pracy awaryjnej testu można połączyć się z testem po awarii za pomocą maszyny wirtualnej i sprawdzić poprawność pracy awaryjnej testu.

Po zakończeniu testowania na komputerze wirtualnym po awarii, oczyścić kopię testową, uruchamiając operację pracy awaryjnej testu oczyszczania. Ta operacja usuwa testową kopię maszyny wirtualnej, która została utworzona przez test pracy awaryjnej.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

Przejmując maszynę wirtualną w trybie fail over do określonego punktu odzyskiwania.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Po pomyślnym zakończeniu zadania pracy awaryjnej można zatwierdzić operację trybu failover.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Ponowne przekładanie i powrót po awarii do regionu źródłowego

Po przełączeniu w tryb failover, gdy będzie można wrócić do oryginalnego regionu, rozpocznij replikację wsteczną dla elementu chronionego replikacją przy użyciu `Update-AzRecoveryServicesAsrProtectionDirection` polecenia cmdlet.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Po zakończeniu ponownego wycofywania można przejść awaryjnie w odwrotnym kierunku, zachodnie stany USA do wschodnich stanów USA i powrót po awarii do regionu źródłowego.

## <a name="disable-replication"></a>Wyłączanie replikacji

Replikację można wyłączyć `Remove-AzRecoveryServicesAsrReplicationProtectedItem` za pomocą polecenia cmdlet.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Następne kroki

Wyświetl [odwołanie programu PowerShell do odzyskiwania usługi Azure Site Recovery,](/powershell/module/az.RecoveryServices) aby dowiedzieć się, jak wykonywać inne zadania, takie jak tworzenie planów odzyskiwania i testowanie pracy awaryjnej planów odzyskiwania za pomocą programu PowerShell.
