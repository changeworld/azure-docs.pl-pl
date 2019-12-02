---
title: Konfigurowanie kluczy zarządzanych przez klienta za pomocą Azure Key Vault przy użyciu programu PowerShell — Azure Storage
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta do szyfrowania za pomocą usługi Azure Storage przy użyciu programu PowerShell. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bd723787d9cea2d3b9d81ae9db63c70a21190854
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666226"
---
# <a name="configure-customer-managed-keys-for-azure-storage-by-using-powershell"></a>Konfigurowanie kluczy zarządzanych przez klienta usługi Azure Storage przy użyciu programu PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule opisano sposób konfigurowania Azure Key Vault za pomocą kluczy zarządzanych przez klienta przy użyciu programu PowerShell. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu programu PowerShell](../../key-vault/quick-create-powershell.md).

> [!IMPORTANT]
> Przy użyciu kluczy zarządzanych przez klienta z szyfrowaniem usługi Azure Storage wymagane są dwie właściwości w magazynie kluczy, **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości nie są domyślnie włączone. Aby włączyć te właściwości, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
> Obsługiwane są tylko klucze RSA i rozmiar klucza 2048.

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Aby włączyć klucze zarządzane przez klienta dla konta magazynu, należy najpierw przypisać tożsamość zarządzaną przez system do konta magazynu. Ta tożsamość zarządzana zostanie użyta do nadania uprawnień kontu magazynu dostępu do magazynu kluczy.

Aby przypisać tożsamość zarządzaną przy użyciu programu PowerShell, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Aby uzyskać więcej informacji na temat konfigurowania tożsamości zarządzanych przez system przy użyciu programu PowerShell, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Utwórz nowy magazyn kluczy

Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell, wywołaj polecenie [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage musi mieć włączone dwa ustawienia ochrony klucza, **usuwanie nietrwałe** i **nie przeczyszczanie**. 

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku zostanie użyta zarządzana tożsamość, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj polecenie [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Utwórz nowy klucz

Następnie utwórz nowy klucz w magazynie kluczy. Aby utworzyć nowy klucz, wywołaj polecenie [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage używa kluczy zarządzanych przez firmę Microsoft. W tym kroku Skonfiguruj konto usługi Azure Storage tak, aby korzystało z kluczy zarządzanych przez klienta, i określ klucz, który ma zostać skojarzony z kontem magazynu.

Aby zaktualizować ustawienia szyfrowania konta magazynu, wywołaj metodę [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) . Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby używało nowej wersji. Najpierw należy wywołać polecenie [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) , aby uzyskać najnowszą wersję klucza. Następnie Wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , aby zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji klucza, jak pokazano w poprzedniej sekcji.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
