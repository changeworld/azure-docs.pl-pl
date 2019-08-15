---
title: Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania usługi Azure Storage z poziomu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure skonfigurować klucze zarządzane przez klienta do szyfrowania usługi Azure Storage. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 837e3be209da1fe42ced9e4a23a75c46612cebd2
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036624"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania usługi Azure Storage z poziomu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule pokazano, jak skonfigurować magazyn kluczy z kluczami zarządzanymi przez klienta przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> W przypadku korzystania z kluczy zarządzanych przez klienta z szyfrowaniem usługi Azure Storage wymagane jest skonfigurowanie dwóch wymaganych właściwości magazynu kluczy, **usuwania** nietrwałego i nieprzeczyszczania. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
> Obsługiwane są tylko klucze RSA i rozmiar klucza 2048.

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Aby włączyć klucze zarządzane przez klienta dla konta magazynu, należy najpierw przypisać tożsamość zarządzaną przez system do konta magazynu. Ta tożsamość zarządzana zostanie użyta do nadania uprawnień kontu magazynu dostępu do magazynu kluczy.

Aby przypisać tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Aby uzyskać więcej informacji na temat konfigurowania tożsamości zarządzanych przez system za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Utwórz nowy magazyn kluczy

Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage musi mieć włączone dwa ustawienia ochrony klucza, **usuwanie nietrwałe** i **nie przeczyszczanie**. Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure z włączonymi tymi ustawieniami, uruchom następujące polecenia. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami. 

Aby utworzyć nowy magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ](/cli/azure/keyvault#az-keyvault-create)Key webcreate. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku zostanie użyta zarządzana tożsamość, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj polecenie [AZ KeyBinding Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Utwórz nowy klucz

Następnie Utwórz klucz w magazynie kluczy. Aby utworzyć klucz, wywołaj [AZ Key magazynu Create](/cli/azure/keyvault/key#az-keyvault-key-create). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage używa kluczy zarządzanych przez firmę Microsoft. Skonfiguruj konto usługi Azure Storage dla kluczy zarządzanych przez klienta i określ klucz, który ma zostać skojarzony z kontem magazynu.

Aby zaktualizować ustawienia szyfrowania konta magazynu, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update). W tym przykładzie zawarto również zapytania dotyczące identyfikatora URI i wersji klucza magazynu kluczy, obie wartości są konieczne do skojarzenia klucza z kontem magazynu. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby używało nowej wersji. Najpierw należy wykonać zapytanie dotyczące identyfikatora URI magazynu kluczy przez wywołanie [AZ Key magazyn show](/cli/azure/keyvault#az-keyvault-show)i dla wersji klucza przez wywołanie [AZ Key magazynu list-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Następnie Wywołaj [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , aby zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji klucza, jak pokazano w poprzedniej sekcji.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md) 
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
