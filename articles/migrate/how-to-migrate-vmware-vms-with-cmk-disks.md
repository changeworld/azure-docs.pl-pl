---
title: Migrowanie maszyn wirtualnych VMware na platformę Azure za pomocą szyfrowania po stronie serwera (SSE) i kluczy zarządzanych przez klienta (CMK) przy użyciu migracji serwera migracji usługi Azure
description: Dowiedz się, jak migrować maszyny wirtualne VMware na platformę Azure za pomocą szyfrowania po stronie serwera (SSE) i kluczy zarządzanych przez klienta (CMK) przy użyciu migracji serwera migracji usługi Azure
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269486"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrowanie maszyn wirtualnych VMware na maszyny wirtualne platformy Azure z włączoną obsługą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta

W tym artykule opisano sposób migracji maszyn wirtualnych VMware na maszyny wirtualne platformy Azure z dyskami zaszyfrowanymi przy użyciu szyfrowania po stronie serwera (SSE) z kluczami zarządzanymi przez klienta (CMK), przy użyciu migracji serwera migracji usługi Azure (replikacja bezagentowa).

Środowisko portalu migracji serwera migracji migracji usługi Azure Migrate Server umożliwia [migrację maszyn wirtualnych VMware na platformę Azure za pomocą replikacji bez agenta.](tutorial-migrate-vmware.md) Środowisko portalu obecnie nie oferuje możliwość włączenia SSE z CMK dla dysków replikowanych na platformie Azure. Możliwość włączania SSE z cmk dla dysków replikowanych jest obecnie dostępna tylko za pośrednictwem interfejsu API REST. W tym artykule zobaczysz, jak utworzyć i wdrożyć [szablon usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md) do replikowania maszyny Wirtualnej VMware i konfigurowania replikowanych dysków na platformie Azure do używania SSE z cmk.

Przykłady w tym artykule używają [programu Azure PowerShell](/powershell/azure/new-azureps-module-az) do wykonywania zadań potrzebnych do utworzenia i wdrożenia szablonu Menedżera zasobów.

[Dowiedz się więcej](../virtual-machines/windows/disk-encryption.md) o szyfrowaniu po stronie serwera (SSE) za pomocą kluczy zarządzanych przez klienta (CMK) dla dysków zarządzanych.

## <a name="prerequisites"></a>Wymagania wstępne

- Zapoznaj się z [samouczkiem](tutorial-migrate-vmware.md) na temat migracji maszyn wirtualnych VMware na platformę Azure za pomocą replikacji bez agenta, aby zrozumieć wymagania dotyczące narzędzi.
- [Postępuj zgodnie z tymi instrukcjami,](how-to-add-tool-first-time.md) aby utworzyć projekt migracji platformy Azure i dodać narzędzie **migracji platformy Azure: Migracja serwera** do projektu.
- [Postępuj zgodnie z tymi instrukcjami,](how-to-set-up-appliance-vmware.md) aby skonfigurować urządzenie migracji platformy Azure dla oprogramowania VMware w środowisku lokalnym i pełne odnajdowanie.

## <a name="prepare-for-replication"></a>Przygotowanie do replikacji

Po zakończeniu odnajdowania maszyn wirtualnych linia Odnalezione serwery na kafelku Migracja serwera będzie wyświetlać liczbę maszyn wirtualnych VMware wykrytych przez urządzenie.

Przed rozpoczęciem replikacji maszyn wirtualnych, infrastruktury replikacji musi być przygotowany.

1. Utwórz wystąpienie usługi Service Bus w regionie docelowym. Usługa Service Bus jest używana przez lokalne urządzenie migracji platformy Azure do komunikowania się z usługą migracji serwera w celu koordynowania replikacji i migracji.
2. Utwórz konto magazynu do przenoszenia dzienników operacji z replikacji.
3. Utwórz konto magazynu, do które urządzenie migracji platformy Azure przekazuje dane replikacji.
4. Utwórz magazyn kluczy i skonfiguruj magazyn kluczy do zarządzania tokenami podpisu dostępu współdzielonego dla dostępu do obiektów blob na kontach magazynu utworzonych w kroku 3 i 4.
5. Wygeneruj token podpisu dostępu współdzielonego dla magistrali usług utworzonej w kroku 1 i utwórz klucz tajny dla tokenu w magazynie kluczy utworzonym w poprzednim kroku.
6. Utwórz zasady dostępu usługi Key Vault, aby zapewnić lokalnemu urządzeniu migracji platformy Azure (przy użyciu aplikacji AAD urządzenia) i usłudze migracji serwera dostęp do usługi Key Vault.
7. Utwórz zasady replikacji i skonfiguruj usługę migracji serwera ze szczegółami infrastruktury replikacji utworzonej w poprzednim kroku.

Infrastruktura replikacji musi zostać utworzona w docelowym regionie platformy Azure dla migracji i w docelowej subskrypcji platformy Azure, do których są migrowane maszyny wirtualne.

Środowisko portalu migracji serwera upraszcza przygotowanie infrastruktury replikacji, automatycznie wykonując to w celu uzyskania replikacji maszyny wirtualnej po raz pierwszy w projekcie. W tym artykule zakładamy, że masz już zreplikowane co najmniej jedną maszynę wirtualną przy użyciu środowiska portalu i że infrastruktura replikacji jest już utworzona. Przyjrzymy się, jak odnajdywać szczegóły istniejącej infrastruktury replikacji i jak używać tych szczegółów jako danych wejściowych do szablonu Menedżera zasobów, który będzie używany do konfigurowania replikacji za pomocą cmk.

### <a name="identifying-replication-infrastructure-components"></a>Identyfikowanie składników infrastruktury replikacji

1. W witrynie Azure portal przejdź do strony grupy zasobów i wybierz grupę zasobów, w której utworzono projekt migracji platformy Azure.
2. Wybierz **wdrożeń** z lewego menu i wyszukaj nazwę wdrożenia rozpoczynającą się od ciągu *"Microsoft.MigrateV2.VMwareV2EnableMigrate"*. Zostanie wyświetlona lista szablonów Menedżera zasobów utworzonych przez środowisko portalu w celu skonfigurowania replikacji maszyn wirtualnych w tym projekcie. Pobierzemy jeden taki szablon i użyjemy go jako podstawy do przygotowania szablonu do replikacji za pomocą cmk.
3. Aby pobrać szablon, wybierz dowolne wdrożenie pasujące do wzorca ciągu w poprzednim kroku, > wybierz **szablon** z lewego menu > Kliknij przycisk **Pobierz** z górnego menu. Zapisz plik template.json lokalnie. Edytujesz ten plik szablonu w ostatnim kroku.

## <a name="create-a-disk-encryption-set"></a>Tworzenie zestawu szyfrowania dysku

Obiekt zestawu szyfrowania dysku mapuje dyski zarządzane do magazynu kluczy, który zawiera cmk do użycia dla SSE. Aby replikować maszyny wirtualne za pomocą współrzędnościowej maszyny pomiarowej, utworzysz zestaw szyfrowania dysku i przekażesz go jako dane wejściowe do operacji replikacji.

Postępuj zgodnie z przykładem [tutaj,](../virtual-machines/windows/disk-encryption.md#powershell) aby utworzyć zestaw szyfrowania dysku przy użyciu programu Azure PowerShell. Upewnij się, że zestaw szyfrowania dysku jest tworzony w subskrypcji docelowej, do których są migrowane maszyny wirtualne, oraz w docelowym regionie platformy Azure dla migracji.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Uzyskaj szczegółowe informacje o maszynie wirtualnej VMware do migracji

W tym kroku użyjesz programu Azure PowerShell, aby uzyskać szczegółowe informacje o maszynie wirtualnej, która musi zostać zmigrowana. Te szczegóły będą używane do konstruowania szablonu Menedżera zasobów dla replikacji. W szczególności dwie interesujące właściwości to:

- Identyfikator zasobu komputera dla odnalezionych maszyn wirtualnych.
- Lista dysków dla maszyny Wirtualnej i ich identyfikatorów dysków.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Skopiuj wartość ciągu SiteId odpowiadającą urządzeniu migracji platformy Azure, za pośrednictwem których zostanie wykryta maszyna wirtualna. W przykładzie pokazanym powyżej Identyfikator witryny to *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be669/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Skopiuj resourceid, nazwę i wartości uuid dysku dla komputera, który ma zostać zmigrowany.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Tworzenie szablonu Menedżera zasobów dla replikacji

- Otwórz plik szablonu Menedżera zasobów pobrany w kroku **Identyfikowanie składników infrastruktury replikacji** w wybranym edytorze.
- Usuń wszystkie definicje zasobów z szablonu z wyjątkiem zasobów typu *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Jeśli istnieje wiele definicji zasobów powyższego typu, usuń wszystkie oprócz jednego. Usuń wszelkie **dependsOn** definicje właściwości z definicji zasobu.
- Na końcu tego kroku powinien mieć plik, który wygląda jak w poniższym przykładzie i ma ten sam zestaw właściwości.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Edytuj właściwość **name** w definicji zasobu. Zastąp ciąg, który następuje po ostatnim "/" we właściwości name z wartością *$machine. Nazwa*(z poprzedniego kroku).
- Zmień wartość **właściwości properties.providerSpecificDetails.vmwareMachineId** o wartości *$machine. Identyfikator zasobu*( z poprzedniego kroku).
- Ustaw wartości dla **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** na identyfikator docelowej grupy zasobów, docelowy identyfikator zasobu sieci wirtualnej i nazwę podsieci docelowej.
- Ustaw wartość **licenseType** na "WindowsServer", aby zastosować korzyść hybrydową platformy Azure dla tej maszyny Wirtualnej. Jeśli ta maszyna wirtualna nie kwalifikuje się do korzystania z usługi Azure Hybrid Benefit, ustaw wartość **licenseType** na NoLicenseType.
- Zmień wartość właściwości **targetVmName** na żądaną nazwę maszyny wirtualnej platformy Azure dla zmigrowanych maszyn wirtualnych.
- Opcjonalnie dodaj właściwość o nazwie **targetVmSize** poniżej właściwości **targetVmName.** Ustaw wartość właściwości **targetVmSize** na żądany rozmiar maszyny wirtualnej platformy Azure dla zmigrowanych maszyn wirtualnych.
- Właściwość **disksToInclude** jest listą danych wejściowych dysku do replikacji z każdym elementem listy reprezentującym jeden dysk lokalny. Utwórz tyle elementów listy, ile liczba dysków na lokalnej maszynie wirtualnej. Zastąp właściwość **diskId** w elemencie listy na uuid dysków zidentyfikowanych w poprzednim kroku. Ustaw wartość **isOSDisk** na "true" dla dysku systemu operacyjnego maszyny Wirtualnej i "false" dla wszystkich innych dysków. Pozostaw **logStorageAccountId** i **logStorageAccountSasSecretName** właściwości bez zmian. Ustaw wartość **diskType** na typ dysku zarządzanego platformy Azure *(Standard_LRS, Premium_LRS, StandardSSD_LRS*) do użycia na dysku. W przypadku dysków, które muszą być szyfrowane za pomocą cmk, dodaj właściwość o nazwie **diskEncryptionSetId** i ustaw wartość identyfikatora zasobu utworzonego zestawu szyfrowania dysku(**$des. Identyfikator**) w kroku *Tworzenie zestawu szyfrowania dysku*
- Zapisz edytowany plik szablonu. W powyższym przykładzie edytowany plik szablonu wygląda następująco:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Konfigurowanie replikacji

Teraz można wdrożyć edytowany szablon Menedżera zasobów w grupie zasobów projektu, aby skonfigurować replikację dla maszyny Wirtualnej. Dowiedz się, jak [wdrożyć zasób za pomocą szablonów usługi Azure Resource Manager i programu Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Następne kroki

[Monitoruj](tutorial-migrate-vmware.md#track-and-monitor) stan replikacji za pośrednictwem portalu i wykonuj migracje testowe i migrację.
