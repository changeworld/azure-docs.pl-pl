---
title: Konfigurowanie odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure przy użyciu programu PowerShell w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować replikację i tryb failover na platformę Azure do odzyskiwania po awarii maszyn wirtualnych programu VMware przy użyciu programu PowerShell w usłudze Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 04/08/2019
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: 5490149f199c2d7887716ceae3f035527ad33961
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170052"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure przy użyciu programu PowerShell

W tym artykule zobaczysz, jak replikować i trybu failover maszyny wirtualne VMware na platformę Azure przy użyciu programu Azure PowerShell.

Omawiane kwestie:

> [!div class="checklist"]
> - Utwórz magazyn usługi Recovery Services i Ustaw kontekst magazynu.
> - Sprawdź poprawność rejestracji serwera w magazynie.
> - Skonfiguruj replikację, łącznie z zasadami replikacji. Dodawanie serwera vCenter i odnajdywanie maszyn wirtualnych.
> - Dodawanie serwera vCenter i odnajdywanie
> - Tworzenie konta magazynu do przechowywania danych replikacji i zreplikować maszyny wirtualne.
> - Wykonaj przejście w tryb failover. Konfigurowanie ustawień trybu failover, wykonaj ustawienia dotyczące replikowania maszyn wirtualnych.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Przeanalizuj informacje o [składnikach i architekturze scenariusza](vmware-azure-architecture.md).
- Zapoznaj się z [wymaganiami dotyczącymi obsługi](site-recovery-support-matrix-to-azure.md) wszystkich składników.
- Masz programu Azure PowerShell `Az` modułu. Jeśli potrzebujesz zainstalować lub uaktualnić programu Azure PowerShell, postępuj zgodnie z tym [Przewodnik instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Logowanie się do platformy Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia cmdlet Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Wybierz subskrypcję platformy Azure, o których mają być replikowane do maszyn wirtualnych VMware. Aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp do, użyj polecenia cmdlet Get-AzSubscription. Wybierz subskrypcję platformy Azure, które chcesz pracować przy użyciu polecenia cmdlet Select AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Konfigurowanie magazynu usługi Recovery Services

1. Utwórz grupę zasobów, w której chcesz utworzyć magazyn usługi Recovery Services. W poniższym przykładzie grupa zasobów nosi nazwę VMwareDRtoAzurePS i jest tworzone w regionie Azja Wschodnia.

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

2. Utwórz magazyn usługi Recovery services. W poniższym przykładzie magazynu usługi Recovery services o nazwie VMwareDRToAzurePs i jest tworzona w regionie Azja Wschodnia i grupy zasobów utworzonej w poprzednim kroku.

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

3. Pobierz klucz rejestracji magazynu dla magazynu. Klucz rejestracji magazynu umożliwia rejestrowanie serwera konfiguracji w środowisku lokalnym, w tym magazynie. Rejestracja jest częścią procesu instalacji oprogramowania serwera konfiguracji.

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

4. Użyj klucza rejestracji pobranego magazynu i wykonaj kroki opisane w artykułach z podanych poniżej w celu ukończenia instalacji i rejestracji serwera konfiguracji.
   - [Wybranie celów ochrony](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Konfigurowanie środowiska źródłowego](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

Ustaw kontekst magazynu za pomocą polecenia cmdlet Set-ASRVaultContext. Po ustawieniu kolejne operacje usługi Azure Site Recovery w sesji programu PowerShell są wykonywane w kontekście wybranego magazynu.

> [!TIP]
> Moduł Azure PowerShell odzyskiwania lokacji (moduł Az.RecoveryServices) jest dostarczany za pomocą łatwych w użyciu aliasów dla większości poleceń cmdlet. Polecenia cmdlet w module mieć postać  *\<operacji >-**AzRecoveryServicesAsr**\<obiektu >* i mieć równoważne aliasy, które będzie mieć postać  *\< Operacja >-**ASR**\<obiektu >* . W tym artykule używa aliasy polecenia cmdlet w celu ułatwienia odczytu.

W poniższym przykładzie, szczegóły magazynu z $vault zmienna służy do określania kontekst magazynu dla sesji programu PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Jako alternatywę do polecenia cmdlet Set-ASRVaultContext jeden również użyć polecenia cmdlet Import AzRecoveryServicesAsrVaultSettingsFile, aby ustawić kontekst magazynu. Określ ścieżkę, w którym plik klucza rejestracji magazynu znajduje się jako parametru - path, do polecenia cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile. Na przykład:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Kolejne sekcje w tym artykule przyjęto założenie, czy kontekst magazynu dla operacji usługi Azure Site Recovery został ustawiony.

## <a name="validate-vault-registration"></a>Sprawdź poprawność rejestracji w magazynie

W tym przykładzie mamy następujące czynności:

- Serwer konfiguracji (**ConfigurationServer**) został zarejestrowany w tym magazynie.
- Dodatkowym serwerze przetwarzania (**ProcessServer skalowania**) został zarejestrowany do *ConfigurationServer*
- Konta (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) zostało skonfigurowane na serwerze konfiguracji. Te konta są używane do dodawania serwera vCenter, aby odnaleźć maszyny wirtualne i instalacji wypychanej oprogramowania usługi mobilności na serwerach Windows i Linux, które mają być replikowane.

1. Konfiguracja zarejestrowane serwery są reprezentowane przez obiekt sieci szkieletowej w usłudze Site Recovery. Pobierz listę sieci szkieletowej obiektów w magazynie i identyfikowanie serwera konfiguracji.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-ASRFabric
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

2. Określenie serwerów przetwarzania, które mogą służyć do replikowania maszyn.

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

   W powyższych danych wyjściowych ***$ProcessServers [0]*** odpowiada *ProcessServer skalowania* i ***$ProcessServers [1]*** odnosi się do roli serwera przetwarzania na *ConfigurationServer*

3. Określ konta, które zostały skonfigurowane na serwerze konfiguracji.

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

   W powyższych danych wyjściowych ***$AccountHandles [0]*** odnosi się do konta *vCenter_account*, ***$AccountHandles [1]*** kontu *WindowsAccount*, i ***$AccountHandles [2]*** kontu *LinuxAccount*

## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

W tym kroku tworzone są dwie zasady replikacji. Jedne zasady, aby replikować maszyny wirtualne VMware do platformy Azure, a druga do replikowania przełączone w tryb failover maszyn wirtualnych działających na platformie Azure z powrotem do lokacji programu VMware w środowisku lokalnym.

> [!NOTE]
> Większość operacji usługi Azure Site Recovery są wykonywane asynchronicznie. Po zainicjowaniu operacji przesyłania zadania usługi Azure Site Recovery i zwracany jest zadaniem śledzenia obiektu. To zadanie obiektowi śledzenia może służyć do monitorowania stanu operacji.

1. Tworzenie zasad replikacji o nazwie *ReplicationPolicy* na maszyny wirtualne VMware można replikować na platformę Azure przy użyciu określonej właściwości.

   ```azurepowershell
   $Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

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

2. Tworzenie zasad replikacji na potrzeby powrotu po awarii z platformy Azure do lokacji programu VMware w środowisku lokalnym.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Szczegóły zadania w *$Job_FailbackPolicyCreate* do śledzenia zakończenia operacji.

   * Utwórz mapowanie kontenera ochrony, aby zamapować zasady replikacji z serwerem konfiguracji.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

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
    $Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Dodawanie serwera vCenter i odnajdywanie maszyn wirtualnych

Dodaj serwer vCenter za pomocą adresu IP lub nazwy hosta. **— Port** parametr określa port, na serwerze vCenter, aby nawiązać połączenie, **— nazwa** parametr określa przyjazną nazwę dla serwera vCenter i **— konta** parametr określa uchwyt konta na serwerze konfiguracji, można użyć do odnalezienia maszyn wirtualnych zarządzanych przez serwer vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

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

## <a name="create-storage-accounts-for-replication"></a>Tworzenie konta usługi storage dla replikacji

W tym kroku są tworzone konta magazynu, który ma być używany do replikacji. Te konta magazynu są używane później do replikowania maszyn wirtualnych. Upewnij się, że konta magazynu są tworzone w tym samym regionie platformy Azure co magazyn. Możesz pominąć ten krok, jeśli planujesz użyć istniejącego konta magazynu na potrzeby replikacji.

> [!NOTE]
> Podczas replikowania lokalnych maszyn wirtualnych do konta magazynu premium storage, należy określić dodatkowy magazyn standardowy konto (konto magazynu dzienników). Konto magazynu dzienników przechowuje jako pośredni magazyn dzienników replikacji, dopóki Dzienniki mogą być stosowane w elemencie docelowym magazynu premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikacja maszyn wirtualnych VMware

Trwa około 15 – 20 minut dla maszyn wirtualnych, które mają zostać odnalezione z serwera vCenter. Po odnalezieniu obiekt elementu z możliwością objęcia ochroną jest tworzony w usłudze Azure Site Recovery dla każdej odnalezionej maszyny wirtualnej. W tym kroku trzech spośród odnalezionych maszyn wirtualnych są replikowane do kont usługi Azure Storage utworzonej w poprzednim kroku.

Potrzebne są następujące szczegóły, aby chronić odnalezione maszyny wirtualnej:

* Element z możliwością ochrony powinny być replikowane.
* Konto magazynu do replikowania maszyny wirtualnej. Ponadto magazyn dzienników jest konieczna ochrona maszyn wirtualnych na konto magazynu premium storage.
* Serwer przetwarzania, który ma być używany do replikacji. Na liście serwerów dostępnych proces został pobierane i zapisywane w ***$ProcessServers [0]***  *(skalowania ProcessServer)* i ***$ProcessServers [1]*** *(ConfigurationServer)* zmiennych.
* Konto na potrzeby instalacji wypychanej oprogramowania usługi mobilności na maszynach. Lista dostępnych kont została pobrane i przechowywane w ***$AccountHandles*** zmiennej.
* Mapowanie kontenera ochrony grupy replikacji ma być używany do replikacji.
* Grupa zasobów, w którym należy utworzyć maszyny wirtualne w trybie failover.
* Opcjonalnie sieć wirtualną platformy Azure i podsieć, z którą nieudane przez maszynę wirtualną powinien być połączony.

Replikuj teraz następujące maszyny wirtualne przy użyciu ustawień określonych w tej tabeli


|Maszyna wirtualna  |Serwer przetwarzania        |Konto magazynu              |Konto magazynu dzienników  |Zasady           |Konto instalacji usługi Mobility|Docelowa grupa zasobów  | Docelowa sieć wirtualna  |Podsieć docelowa  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| ND                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| ND                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableReplication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Po pomyślnym ukończeniu działania Zadanie włączania replikacji, Replikacja początkowa została uruchomiona dla maszyn wirtualnych. Początkowa replikacja może zająć trochę czasu w zależności od ilości danych do replikacji oraz przepustowość dostępną dla replikacji. Po zakończeniu replikacji początkowej, maszyna wirtualna zostanie przeniesiona do stanu chronionego. Gdy maszyna wirtualna osiągnie stanu chronionego, które można wykonać test trybu failover dla maszyny wirtualnej, należy go dodać do planów odzyskiwania itp.

Można sprawdzić stan replikacji i kondycję replikacji maszyny wirtualnej za pomocą polecenia cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurowanie ustawień trybu failover

Ustawienia trybu failover dla chronionych maszyn może zostać zaktualizowana przy użyciu polecenia cmdlet Set-ASRReplicationProtectedItem. Niektóre ustawienia, które mogą być aktualizowane za pomocą tego polecenia cmdlet są:
* Nazwa maszyny wirtualnej, która ma zostać utworzony w trybie failover
* Rozmiar maszyny wirtualnej, która ma zostać utworzony w trybie failover maszyny Wirtualnej
* Sieć wirtualna platformy Azure i podsieć, która powinna być połączona kart sieciowych maszyny wirtualnej w trybie failover
* Tryb failover do usługi managed disks
* Zastosuj korzyść użycia hybrydowego platformy Azure
* Przypisz statyczny adres IP z docelowa sieć wirtualna ma być przypisane do maszyny wirtualnej w trybie failover.

W tym przykładzie firma Microsoft aktualizuje rozmiar maszyny Wirtualnej maszyny wirtualnej, która ma zostać utworzony w trybie failover dla maszyny wirtualnej *Win2K12VM1* i określić, że użycie maszyny wirtualnej zarządzane dyski w tryb failover.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
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

1. Uruchamianie próbnego odzyskiwania po awarii (test trybu failover) w następujący sposób:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Po zadania testowego trybu failover zakończy się pomyślnie, można zauważyć, że maszyna wirtualna sufiks *"-Test"* (w tym przypadku Win2K12VM1-Test) do jego nazwa jest tworzony na platformie Azure.
3. Można teraz nawiązać połączenia testowego w trybie Failover maszyny wirtualnej i sprawdź poprawność testowy tryb failover.
4. Wyczyść test pracy awaryjnej, za pomocą polecenia cmdlet Start-ASRTestFailoverCleanupJob. Ta operacja usuwa maszynę wirtualną utworzoną w ramach operacji trybu failover testu.

   ```azurepowershell
   $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Failover na platformie Azure

W tym kroku, możemy przełączyć w tryb failover maszyny wirtualnej Win2K12VM1 określony punkt odzyskiwania.

1. Pobierz listę dostępnych punktów odzyskiwania na potrzeby pracy w trybie failover:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Po przełączone w tryb failover pomyślnie, należy można Zatwierdź operację trybu failover i skonfiguruj replikacja odwrotna z platformy Azure z powrotem do lokacji programu VMware w środowisku lokalnym.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak zautomatyzować większej liczby zadań przy użyciu [dokumentacja programu PowerShell odzyskiwania witryn Azure](https://docs.microsoft.com/powershell/module/Az.RecoveryServices).
