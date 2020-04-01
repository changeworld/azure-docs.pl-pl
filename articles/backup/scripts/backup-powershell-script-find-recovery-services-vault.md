---
title: Skrypt programu PowerShell — znajdowanie konta vault for storage
description: Dowiedz się, jak użyć skryptu programu Azure PowerShell, aby znaleźć magazyn usług odzyskiwania, w którym jest zarejestrowane konto magazynu.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76775361"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Skrypt programu Powershell w celu znalezienia magazynu usług odzyskiwania, w którym jest zarejestrowane konto magazynu

Ten skrypt pomaga znaleźć magazyn usług odzyskiwania, w którym jest zarejestrowane konto magazynu.

## <a name="sample-script"></a>Przykładowy skrypt

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Jak wykonać skrypt

1. Zapisz skrypt powyżej na komputerze z wybraną nazwą. W tym przykładzie zapisaliśmy go jako *FindRegisteredStorageAccount.ps1*.
2. Wykonaj skrypt, podając następujące parametry:

    * **-ResourceGroupName** — grupa zasobów konta magazynu
    * **-StorageAccountName** - Nazwa konta magazynu
    * **-SubscriptionID** — identyfikator subskrypcji, w której znajduje się konto magazynu.

W poniższym przykładzie próbuje znaleźć magazyn usług odzyskiwania, w którym jest zarejestrowane konto magazynu *afsaccount:*

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Dane wyjściowe

Dane wyjściowe będą wyświetlane pełną ścieżkę magazynu usług odzyskiwania, gdzie konto magazynu jest zarejestrowany. Oto przykładowe dane wyjściowe:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wykonać kopię zapasową udziałów plików platformy Azure w witrynie Azure portal](https://docs.microsoft.com/azure/backup/backup-afs)
