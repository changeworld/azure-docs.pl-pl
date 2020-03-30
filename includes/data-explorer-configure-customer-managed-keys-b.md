---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297879"
---
## <a name="create-a-new-key-vault"></a>Tworzenie nowego magazynu kluczy

Aby utworzyć nowy magazyn kluczy za pomocą programu PowerShell, należy wywołać [new-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta dla szyfrowania Usługi Azure Data Explorer musi mieć włączone dwa ustawienia ochrony kluczy: **Usuwanie nietrwałe** i **Nie czyścić**. Zastąp wartości zastępcze w nawiasach własnymi wartościami w poniższym przykładzie.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu do magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, tak aby klaster miał uprawnienia dostępu do niego. W tym kroku użyjesz tożsamości zarządzanej przypisanej przez system, która została wcześniej przypisana do klastra. Aby ustawić zasady dostępu dla magazynu kluczy, zadzwoń [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Zastąp wartości zastępcze w nawiasach własnymi wartościami i użyj zmiennych zdefiniowanych w poprzednich przykładach.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Tworzenie nowego klucza

Następnie utwórz nowy klucz w magazynie kluczy. Aby utworzyć nowy klucz, zadzwoń do [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Zastąp wartości zastępcze w nawiasach własnymi wartościami i użyj zmiennych zdefiniowanych w poprzednich przykładach.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
