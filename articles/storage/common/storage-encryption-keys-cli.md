---
title: Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage z wiersza polecenia platformy Azure
description: Dowiedz się, jak skonfigurować kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage przy użyciu wiersza polecenia platformy Azure. Kluczy zarządzanych przez klienta umożliwiają tworzenie, obracanie, wyłącz i odwołać kontroli dostępu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dc9f660ddf7cd003ac113725a9bf7b66189807b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593595"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage z wiersza polecenia platformy Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule przedstawiono sposób konfigurowania magazynu kluczy przy użyciu kluczy zarządzanych przez klienta przy użyciu wiersza polecenia platformy Azure.

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisz tożsamość do konta magazynu

Aby włączyć kluczy konta magazynu zarządzanych przez klienta, należy najpierw przypisać przypisany systemowo tożsamości zarządzanych do konta magazynu. Użyjesz tej tożsamości zarządzanej do udzielania uprawnień do konta magazynu do dostępu do magazynu kluczy.

Aby przypisać tożsamość zarządzana przy użyciu wiersza polecenia platformy Azure, należy wywołać [aktualizacja konta magazynu az](/cli/azure/storage/account#az-storage-account-update). Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Aby uzyskać więcej informacji o konfigurowaniu przypisany systemowo zarządzanych tożsamości za pomocą wiersza polecenia platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na Maszynie wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Utwórz nowy magazyn kluczy

Magazyn kluczy, która umożliwia przechowywanie kluczy zarządzanych przez klienta usługi Azure Storage szyfrowania musi mieć dwa ustawienia ochrony kluczy włączone, **usuwanie nietrwałe** i **przeczyszczanie**. Aby utworzyć nowego magazynu kluczy przy użyciu programu PowerShell lub wiersza polecenia platformy Azure przy użyciu tych ustawień, włączone, wykonaj następujące polecenia. Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami. 

Aby utworzyć nowego magazynu kluczy przy użyciu wiersza polecenia platformy Azure, wywołaj [tworzenie az keyvault](/cli/azure/keyvault#az-keyvault-create). Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu magazynu kluczy

Następnie skonfiguruj zasady dostępu dla usługi key vault, więc, że konto magazynu nie ma uprawnień dostępu do niego. W tym kroku użyjesz tożsamości zarządzanej, które zostały wcześniej przypisane do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, należy wywołać [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
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

Następnie należy utworzyć klucz w magazynie kluczy. Aby utworzyć klucz, wywołaj [tworzenie az keyvault key](/cli/azure/keyvault/key#az-keyvault-key-create). Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage korzysta z kluczy zarządzanych przez firmę Microsoft. Konfigurowanie konta usługi Azure Storage dla kluczy zarządzanych przez klienta i określ klucz do skojarzenia z kontem magazynu.

Aby zaktualizować ustawienia szyfrowania konta magazynu, należy wywołać [aktualizacja konta magazynu az](/cli/azure/storage/account#az-storage-account-update). W tym przykładzie również wysyła zapytania odnośnie dla magazynu kluczy, identyfikator URI i wersja klucza, obie wartości, które są potrzebne do skojarzenia klucza przy użyciu konta magazynu. Pamiętaj, aby zastąpić symbole zastępcze w nawiasach własnymi wartościami.

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

## <a name="update-the-key-version"></a>Aktualizacja wersji klucza

Kiedy tworzysz nową wersję klucza, należy zaktualizować konta magazynu do nowej wersji. Najpierw wyszukać identyfikator URI magazynu kluczy, wywołując [az keyvault show](/cli/azure/keyvault#az-keyvault-show)i wersję klucza, wywołując [az keyvault klucza listę wersji](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Następnie wywołaj [aktualizacja konta magazynu az](/cli/azure/storage/account#az-storage-account-update) można zaktualizować ustawień szyfrowania konta magazynu do nowej wersji klucza, jak pokazano w poprzedniej sekcji.

## <a name="next-steps"></a>Kolejne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md) 
- [Co to jest usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
