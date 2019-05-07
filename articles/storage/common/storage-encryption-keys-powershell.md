---
title: Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage za pomocą programu PowerShell
description: Dowiedz się, jak skonfigurować kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage za pomocą programu PowerShell. Kluczy zarządzanych przez klienta umożliwiają tworzenie, obracanie, wyłącz i odwołać kontroli dostępu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0eeae1451e77d9000c87b1aff7ad73323e74f7ee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154120"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage za pomocą programu PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule przedstawiono sposób konfigurowania magazynu kluczy przy użyciu kluczy zarządzanych przez klienta za pomocą programu PowerShell.

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisz tożsamość do konta magazynu

Aby włączyć kluczy konta magazynu zarządzanych przez klienta, należy najpierw przypisać przypisany systemowo tożsamości zarządzanych do konta magazynu. Użyjesz tej tożsamości zarządzanej do udzielania uprawnień do konta magazynu do dostępu do magazynu kluczy.

Aby przypisać tożsamość zarządzana przy użyciu programu PowerShell, należy wywołać [AzStorageAccount zestaw](/powershell/module/az.storage/set-azstorageaccount). Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Aby uzyskać więcej informacji o konfigurowaniu przypisany systemowo zarządzanych tożsamości za pomocą programu PowerShell, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Utwórz nowy magazyn kluczy

Aby utworzyć nowego magazynu kluczy przy użyciu programu PowerShell, wywołaj [New AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Magazyn kluczy, która umożliwia przechowywanie kluczy zarządzanych przez klienta usługi Azure Storage szyfrowania musi mieć dwa ustawienia ochrony kluczy włączone, **usuwanie nietrwałe** i **przeczyszczanie**. 

Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu magazynu kluczy

Następnie skonfiguruj zasady dostępu dla usługi key vault, więc, że konto magazynu nie ma uprawnień dostępu do niego. W tym kroku użyjesz tożsamości zarządzanej, które zostały wcześniej przypisane do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, należy wywołać [AzKeyVaultAccessPolicy zestaw](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Pamiętaj, aby zastąpić symbole zastępcze w nawiasach przy użyciu własnych wartości i korzystanie ze zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Utwórz nowy klucz

Następnie utwórz nowy klucz w magazynie kluczy. Aby utworzyć nowy klucz, wywołaj [AzKeyVaultKey Dodaj](/powershell/module/az.keyvault/add-azkeyvaultkey). Pamiętaj, aby zastąpić symbole zastępcze w nawiasach przy użyciu własnych wartości i korzystanie ze zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage korzysta z kluczy zarządzanych przez firmę Microsoft. W tym kroku należy skonfigurować konto usługi Azure Storage w taki sposób, aby używać kluczy zarządzanych przez klienta i określ klucz do skojarzenia z kontem magazynu.

Wywołaj [AzStorageAccount zestaw](/powershell/module/az.keyvault/set-azstorageaccount) można zaktualizować ustawień szyfrowania konta magazynu. Pamiętaj, aby zastąpić symbole zastępcze w nawiasach przy użyciu własnych wartości i korzystanie ze zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aktualizacja wersji klucza

Kiedy tworzysz nową wersję klucza, należy zaktualizować konta magazynu do nowej wersji. Po pierwsze wywołanie [Get AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) można pobrać najnowszą wersję klucza. Następnie wywołaj [AzStorageAccount zestaw](/powershell/module/az.keyvault/set-azstorageaccount) można zaktualizować ustawień szyfrowania konta magazynu do nowej wersji klucza, jak pokazano w poprzedniej sekcji.

## <a name="next-steps"></a>Kolejne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md) 
- [Co to jest usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
