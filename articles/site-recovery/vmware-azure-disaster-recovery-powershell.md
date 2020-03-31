---
title: Konfigurowanie odzyskiwania po awarii usługi VMware przy użyciu programu PowerShell w programie Azure Site Revoery
description: Dowiedz się, jak skonfigurować replikację i przebłaganie awaryjne na platformie Azure w celu odzyskiwania po awarii maszyn wirtualnych vmware przy użyciu programu PowerShell w usłudze Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: d2dfaab3d01ea29b0f9ecba1e9d748415bed2edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257201"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure za pomocą programu PowerShell

W tym artykule zobaczy jak replikować i pracy awaryjnej maszyn wirtualnych VMware do platformy Azure przy użyciu programu Azure PowerShell.

Omawiane kwestie:

> [!div class="checklist"]
> - Utwórz magazyn usług odzyskiwania i ustaw kontekst przechowalni.
> - Sprawdzanie poprawności rejestracji serwera w przechowalni.
> - Konfigurowanie replikacji, w tym zasad replikacji. Dodaj serwer vCenter i odkryj maszyny wirtualne.
> - Dodawanie serwera vCenter i odnajdywanie
> - Tworzenie kont magazynu do przechowywania dzienników replikacji lub danych i replikowanie maszyn wirtualnych.
> - Wykonaj przejście w tryb failover. Skonfiguruj ustawienia trybu failover, wykonaj ustawienia replikacji maszyn wirtualnych.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Przeanalizuj informacje o [składnikach i architekturze scenariusza](vmware-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-azure.md) wszystkich składników.
- Masz moduł programu Azure `Az` PowerShell. Jeśli chcesz zainstalować lub uaktualnić program Azure PowerShell, postępuj zgodnie z tym [przewodnikiem, aby zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Logowanie się do platformy Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia cmdlet Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Wybierz subskrypcję platformy Azure, do której chcesz replikować maszyny wirtualne VMware. Użyj polecenia cmdlet Get-AzSubscription, aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp. Wybierz subskrypcję platformy Azure do pracy przy użyciu polecenia cmdlet Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Konfigurowanie magazynu usługi Recovery Services

1. Utwórz grupę zasobów, w której ma być utworzony magazyn usług odzyskiwania. W poniższym przykładzie grupa zasobów nosi nazwę VMwareDRtoAzurePS i jest tworzona w regionie Azji Wschodniej.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Tworzenie magazynu usług odzyskiwania. W poniższym przykładzie magazyn usług odzyskiwania nosi nazwę VMwareDRToAzurePs i jest tworzony w regionie Azji Wschodniej i w grupie zasobów utworzonej w poprzednim kroku.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Pobierz klucz rejestracji przechowalni dla przechowalni. Klucz rejestracji przechowalni służy do rejestrowania lokalnego serwera konfiguracji w tym magazynie. Rejestracja jest częścią procesu instalacji oprogramowania serwera konfiguracji.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Użyj pobranego klucza rejestracji przechowalni i wykonaj kroki opisane w poniższych artykułach, aby zakończyć instalację i rejestrację serwera konfiguracji.
   - [Wybierz swoje cele ochrony](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Konfigurowanie środowiska źródłowego](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Ustawianie kontekstu przechowalni

Ustaw kontekst przechowalni przy użyciu polecenia cmdlet Set-ASRVaultContext. Po ustawieniu kolejne operacje odzyskiwania witryny platformy Azure w sesji programu PowerShell są wykonywane w kontekście wybranego magazynu.

> [!TIP]
> Moduł programu PowerShell usługi Azure Site Recovery (moduł Az.RecoveryServices) jest wyposażony w łatwe w użyciu aliasy dla większości poleceń cmdlet. Polecenia cmdlet w module przyjmują * \<formę Operacja>-**AzRecoveryServicesAsr**\<Object>* i mają równoważne aliasy, które przyjmują * \<formę Operacja>-**OBIEKT ASR**\<>*. W celu ułatwienia obsługi można zastąpić aliasy poleceń cmdlet.

W poniższym przykładzie szczegóły przechowalni ze zmiennej $vault są używane do określania kontekstu przechowalni dla sesji programu PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Jako alternatywę dla polecenia cmdlet Set-ASRVaultContext można również użyć polecenia cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile, aby ustawić kontekst przechowalni. Określ ścieżkę, przy której znajduje się plik klucza rejestracji przechowalni jako parametr -path do polecenia cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile. Przykład:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Kolejne sekcje tego artykułu zakładają, że kontekst magazynu dla operacji usługi Azure Site Recovery został ustawiony.

## <a name="validate-vault-registration"></a>Sprawdzanie poprawności rejestracji magazynu

W tym przykładzie mamy następujące elementy:

- Serwer konfiguracji **(ConfigurationServer**) został zarejestrowany w tym magazynie.
- Dodatkowy serwer procesów **(ScaleOut-ProcessServer)** został zarejestrowany w *ConfigurationServer*
- Konta **(vCenter_account**, **WindowsAccount**, **LinuxAccount**) zostały skonfigurowane na serwerze konfiguracji. Konta te są używane do dodawania serwera vCenter, do odnajdowania maszyn wirtualnych i wypychania i instalowania oprogramowania usługi mobilności na serwerach Windows i Linux, które mają być replikowane.

1. Zarejestrowane serwery konfiguracji są reprezentowane przez obiekt sieci szkieletowej w programie Site Recovery. Pobierz listę obiektów sieci szkieletowej w przechowalni i zidentyfikuj serwer konfiguracji.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Zidentyfikuj serwery procesów, które mogą służyć do replikowania maszyn.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   Z danych wyjściowych powyżej ***$ProcessServers[0]*** odpowiada *ScaleOut-ProcessServer* i ***$ProcessServers[1]*** odpowiada roli serwera przetwarzania na *ConfigurationServer*

3. Identyfikowanie kont, które zostały skonfigurowane na serwerze konfiguracji.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   Z powyższego wyjścia ***$AccountHandles[0]*** odpowiada *vCenter_account*konta , ***$AccountHandles[1]*** do konta *WindowsAccount*i ***$AccountHandles[2]*** do konta *LinuxAccount*

## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

W tym kroku tworują dwie zasady replikacji. Jedna zasada replikowania maszyn wirtualnych VMware na platformie Azure, a druga w celu replikacji maszyn wirtualnych uruchomionych na platformie Azure z powrotem do lokalnej witryny VMware.

> [!NOTE]
> Większość operacji usługi Azure Site Recovery są wykonywane asynchronicznie. Podczas inicjowania operacji jest przesyłane zadanie usługi Azure Site Recovery i zwracany jest obiekt śledzenia zadań. Ten obiekt śledzenia zadania może służyć do monitorowania stanu operacji.

1. Utwórz zasadę replikacji o nazwie *ReplicationPolicy* do replikacji maszyn wirtualnych VMware na platformie Azure z określonymi właściwościami.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Utwórz zasady replikacji do użycia po awarii z platformy Azure do lokalnej witryny VMware.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Użyj szczegółów zadania w *$Job_FailbackPolicyCreate* do śledzenia operacji do zakończenia.

   * Utwórz mapowanie kontenera ochrony w celu mapowania zasad replikacji za pomocą serwera konfiguracji.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Dodawanie serwera vCenter i odnajdywanie maszyn wirtualnych

Dodaj serwer vCenter według adresu IP lub nazwy hosta. Parametr **-port** określa port na serwerze vCenter, z którego ma się łączyć parametr **-Name** określa przyjazną nazwę używaną dla serwera vCenter, a parametr **-Account** określa dojście do konta na serwerze konfiguracji, którego należy użyć do odnajdowania maszyn wirtualnych zarządzanych przez serwer vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Tworzenie kont magazynu dla replikacji

**Aby zapisać na dysku zarządzanym, należy użyć [modułu Powershell Az.RecoveryServices 2.0.0.](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)** Wymaga tylko utworzenia konta magazynu dziennika. Zaleca się użycie standardowego typu konta i nadmiarowości LRS, ponieważ jest on używany do przechowywania tylko dzienników tymczasowych. Upewnij się, że konto magazynu jest tworzony w tym samym regionie platformy Azure jako przechowalni.

Jeśli używasz wersji modułu Az.RecoveryServices starszej niż 2.0.0, użyj następujących kroków, aby utworzyć konta magazynu. Te konta magazynu są później używane do replikowania maszyn wirtualnych. Upewnij się, że konta magazynu są tworzone w tym samym regionie platformy Azure co magazyn. Ten krok można pominąć, jeśli planujesz użyć istniejącego konta magazynu do replikacji.

> [!NOTE]
> Podczas replikowania lokalnych maszyn wirtualnych do konta magazynu w wersji premium należy określić dodatkowe standardowe konto magazynu (konto magazynu dziennika). Konto magazynu dziennika przechowuje dzienniki replikacji jako magazyn pośredni, dopóki dzienniki nie mogą być stosowane w miejscu docelowym magazynu w jakości.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikowanie maszyn wirtualnych VMware

Trwa około 15-20 minut dla maszyn wirtualnych, które mają być odnajdyne z serwera vCenter. Po wykryciu obiekt elementu chronionego jest tworzony w usłudze Azure Site Recovery dla każdej odnalezionej maszyny wirtualnej. W tym kroku trzy odnalezione maszyny wirtualne są replikowane do kont usługi Azure Storage utworzonych w poprzednim kroku.

Aby chronić odnalezioną maszynę wirtualną, potrzebne będą następujące szczegóły:

* Element chroniony, który ma zostać zreplikowany.
* Konto magazynu do replikacji maszyny wirtualnej do (tylko wtedy, gdy replikujesz do konta magazynu). 
* Magazyn dziennika jest potrzebny do ochrony maszyn wirtualnych na koncie magazynu w wersji premium lub na dysku zarządzanym.
* Serwer przetwarzania, który ma być używany do replikacji. Lista dostępnych serwerów przetwarzania została pobrana i zapisana w zmiennych ***$ProcessServers[0]****(ScaleOut-ProcessServer)* i ***$ProcessServers[1]*** *(ConfigurationServer).*  
* Konto używane do wypychania oprogramowania usługi mobilności na komputerach. Lista dostępnych kont została pobrana i przechowywana w ***zmiennej $AccountHandles.***
* Mapowanie kontenera ochrony dla zasad replikacji, które mają być używane do replikacji.
* Grupa zasobów, w której maszyny wirtualne muszą być tworzone w trybie failover.
* Opcjonalnie należy połączyć sieć wirtualną platformy Azure i podsieć, z którą powinna być połączona maszyna wirtualna po awarii.

Teraz replikuj następujące maszyny wirtualne przy użyciu ustawień określonych w tej tabeli


|Maszyna wirtualna  |Serwer przetwarzania        |Konto magazynu              |Konto magazynu dziennika  |Zasady           |Konto instalacji usługi mobilności|Docelowa grupa zasobów  | Docelowa sieć wirtualna  |Podsieć docelowa  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |Serwer konfiguracji   |Nie dotyczy| konto logstorageaccount1                 |Polityka replikacji|LinuxAccount (konto LinuxAccount)                             |VMwareDRToAzurePs      |ASR-vnet                 |Podsieć-1       |
|Win2K12VM1       |Serwer procesowy ScaleOut|konto premiumstorage1       |konto logstorageaccount1   |Polityka replikacji|Konto systemu Windows                           |VMwareDRToAzurePs      |ASR-vnet                 |Podsieć-1       |   
|CentOSVM2 (CentOSVM2)       |Serwer konfiguracji   |replicationststorageaccount1| Nie dotyczy                 |Polityka replikacji|LinuxAccount (konto LinuxAccount)                             |VMwareDRToAzurePs      |ASR-vnet                 |Podsieć-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Po pomyślnym zakończeniu zadania replikacji włączania replikacja początkowa jest uruchamiana dla maszyn wirtualnych. Replikacja początkowa może chwilę potrwać w zależności od ilości danych do replikacji i przepustowości dostępnej dla replikacji. Po zakończeniu replikacji początkowej maszyna wirtualna przechodzi do stanu chronionego. Gdy maszyna wirtualna osiągnie stan chroniony, można wykonać test pracy awaryjnej dla maszyny wirtualnej, dodać ją do planów odzyskiwania itp.

Stan replikacji i kondycja replikacji maszyny wirtualnej można sprawdzić za pomocą polecenia cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurowanie ustawień trybu failover

Ustawienia trybu failover dla chronionych komputerów można aktualizować za pomocą polecenia cmdlet Set-ASRReplicationProtectedItem. Niektóre ustawienia, które można zaktualizować za pomocą tego polecenia cmdlet, to:
* Nazwa maszyny wirtualnej, która ma zostać utworzona w trybie failover
* Rozmiar maszyny wirtualnej maszyny wirtualnej, który ma zostać utworzony w trybie pracy awaryjnej
* Sieć wirtualna i podsieć platformy Azure, do których powinny być połączone karty sieciowe maszyny wirtualnej podczas pracy awaryjnej
* Przewijowanie awaryjne na dyskach zarządzanych
* Stosowanie korzyści z użycia hybrydowego platformy Azure
* Przypisz statyczny adres IP z docelowej sieci wirtualnej, który ma być przypisany do maszyny wirtualnej w trybie failover.

W tym przykładzie możemy zaktualizować rozmiar maszyny wirtualnej maszyny wirtualnej, które mają zostać utworzone w trybie failover dla maszyny wirtualnej *Win2K12VM1* i określić, że maszyna wirtualna używać dysków zarządzanych w trybie failover.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. Uruchom wiertło odzyskiwania po awarii (test pracy awaryjnej) w następujący sposób:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Po pomyślnym zakończeniu zadania pracy awaryjnej testu, można zauważyć, że sufiks maszyny wirtualnej z *"-Test"* (Win2K12VM1-Test w tym przypadku) do jego nazwy jest tworzony na platformie Azure.
3. Teraz można połączyć się z testem awaryjnym maszyny wirtualnej i sprawdzić poprawność pracy awaryjnej testu.
4. Wyczyść test pracy awaryjnej przy użyciu polecenia cmdlet Start-ASRTestFailoverCleanupJob. Ta operacja usuwa maszynę wirtualną utworzoną w ramach operacji pracy awaryjnej testu.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

W tym kroku możemy awaryjnie maszyny wirtualnej Win2K12VM1 do określonego punktu odzyskiwania.

1. Pobierz listę dostępnych punktów odzyskiwania do użycia w przypadku pracy awaryjnej:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Po pomyślnym zakończeniu pracy awaryjnej można zatwierdzić operację pracy awaryjnej i skonfigurować replikację wsteczną z platformy Azure z powrotem do lokalnej witryny VMware.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zautomatyzować więcej zadań przy użyciu [odwołania programu Azure Site Recovery PowerShell](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).
