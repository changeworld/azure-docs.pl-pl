---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021194"
---
Usługa Azure Eksplorator danych szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych. Klucze zarządzane przez klienta muszą być przechowywane w [Azure Key Vault](/azure/key-vault/key-vault-overview). Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć interfejsu API Azure Key Vault, aby generować klucze. Klaster usługi Azure Eksplorator danych i Magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Szczegółowe wyjaśnienie kluczy zarządzanych przez klienta można znaleźć w temacie [klucze zarządzane przez klienta z Azure Key Vault](/azure/storage/common/storage-service-encryption). W tym artykule opisano sposób konfigurowania kluczy zarządzanych przez klienta.

> [!Note]
> Aby skonfigurować klucze zarządzane przez klienta za pomocą usługi Azure Eksplorator danych, należy [ustawić dwie właściwości magazynu kluczy](/azure/key-vault/key-vault-ovw-soft-delete): **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości nie są domyślnie włączone. Aby włączyć te właściwości, użyj [programu PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) lub [interfejsu wiersza polecenia platformy Azure](/azure/key-vault/key-vault-soft-delete-cli). Obsługiwane są tylko klucze RSA i rozmiar klucza 2048.

## <a name="assign-an-identity-to-the-cluster"></a>Przypisywanie tożsamości do klastra

Aby włączyć klucze zarządzane przez klienta dla klastra, należy najpierw przypisać do klastra zarządzaną tożsamość przypisaną przez system. Ta tożsamość zarządzana zostanie użyta w celu przyznania klastrowi uprawnień dostępu do magazynu kluczy. Aby skonfigurować zarządzane tożsamości przypisane do systemu, zobacz [tożsamości zarządzane](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Utwórz nowy magazyn kluczy

Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell, wywołaj polecenie [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta dla usługi Azure Eksplorator danych Encryption musi mieć włączone dwa ustawienia ochrony klucza, **usuwanie nietrwałe** i **nie przeczyszczanie**. Zastąp wartości symboli zastępczych w nawiasach własnymi wartościami w poniższym przykładzie.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, aby klaster miał uprawnienia dostępu do niego. W tym kroku użyjesz tożsamości zarządzanej przypisanej do systemu, która została wcześniej przypisana do klastra. Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj polecenie [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Zastąp wartości symboli zastępczych w nawiasach własnymi wartościami i użyj zmiennych zdefiniowanych w poprzednich przykładach.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Utwórz nowy klucz

Następnie utwórz nowy klucz w magazynie kluczy. Aby utworzyć nowy klucz, wywołaj polecenie [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Zastąp wartości symboli zastępczych w nawiasach własnymi wartościami i użyj zmiennych zdefiniowanych w poprzednich przykładach.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
