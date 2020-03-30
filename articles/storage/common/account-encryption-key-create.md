---
title: Tworzenie konta obsługującego klucze zarządzane przez klienta dla tabel i kolejek
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć konto magazynu, które obsługuje konfigurowanie kluczy zarządzanych przez klienta dla tabel i kolejek. Użyj interfejsu wiersza polecenia platformy Azure lub szablonu Usługi Azure Resource Manager, aby utworzyć konto magazynu, które opiera się na kluczu szyfrowania konta dla szyfrowania usługi Azure Storage. Następnie można skonfigurować klucze zarządzane przez klienta dla konta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083553"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Tworzenie konta obsługującego klucze zarządzane przez klienta dla tabel i kolejek

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie magazyn kolejek i magazyn tabel używają klucza o zakresie do usługi i zarządzanego przez firmę Microsoft. Można również zdecydować się na użycie kluczy zarządzanych przez klienta do szyfrowania danych kolejki lub tabeli. Aby używać kluczy zarządzanych przez klienta z kolejkami i tabelami, należy najpierw utworzyć konto magazynu, które używa klucza szyfrowania, który jest ograniczony do konta, a nie do usługi. Po utworzeniu konta, które używa klucza szyfrowania konta dla danych kolejki i tabeli, można skonfigurować klucze zarządzane przez klienta za pomocą usługi Azure Key Vault dla tego konta magazynu.

W tym artykule opisano sposób tworzenia konta magazynu, który opiera się na klucz, który jest ograniczony do konta. Po utworzeniu konta firma Microsoft używa klucza konta do szyfrowania danych na koncie, a firma Microsoft zarządza kluczem. Następnie można skonfigurować klucze zarządzane przez klienta dla konta, aby korzystać z tych korzyści, w tym możliwość dostarczania własnych kluczy, aktualizowanie wersji klucza, obracanie kluczy i odwoływanie kontroli dostępu.

## <a name="about-the-feature"></a>Informacje o tej funkcji

Aby utworzyć konto magazynu, które opiera się na klucz szyfrowania konta dla magazynu kolejki i tabel, należy najpierw zarejestrować się, aby użyć tej funkcji na platformie Azure. Ze względu na ograniczoną pojemność, należy pamiętać, że może upłynąć kilka miesięcy, zanim wnioski o dostęp zostaną zatwierdzone.

Konto magazynu, które opiera się na kluczu szyfrowania konta dla magazynu kolejki i tabeli w następujących regionach:

- Wschodnie stany USA
- Południowo-środkowe stany USA
- Zachodnie stany USA 2  

### <a name="register-to-use-the-account-encryption-key"></a>Zarejestruj się, aby użyć klucza szyfrowania konta

Aby zarejestrować się, aby użyć klucza szyfrowania konta z magazynu kolejki lub tabel, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby zarejestrować się w programie PowerShell, wywołanie polecenia [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować się w usłudze Azure CLI, wywołanie polecenia [az feature register.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Szablonu](#tab/template)

Nie dotyczy

---

### <a name="check-the-status-of-your-registration"></a>Sprawdź status swojej rejestracji

Aby sprawdzić stan rejestracji dla magazynu kolejki lub tabel, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby sprawdzić stan rejestracji w programie PowerShell, należy wywołać polecenie [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić stan rejestracji za pomocą interfejsu wiersza polecenia platformy Azure, wywołanie polecenia [funkcji az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Szablonu](#tab/template)

Nie dotyczy

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Ponowne rejestrowanie dostawcy zasobów usługi Azure Storage

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby ponownie zarejestrować dostawcę zasobów.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby ponownie zarejestrować dostawcę zasobów w programie PowerShell, wywołanie polecenia [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ponownie zarejestrować dostawcę zasobów za pomocą interfejsu wiersza polecenia platformy Azure, wywołanie polecenia [rejestru dostawcy az.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Szablonu](#tab/template)

Nie dotyczy

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Tworzenie konta korzystającego z klucza szyfrowania konta

Należy skonfigurować nowe konto magazynu, aby używało klucza szyfrowania konta dla kolejek i tabel w czasie tworzenia konta magazynu. Po utworzeniu konta nie można zmienić zakresu klucza szyfrowania.

Konto magazynu musi być typu ogólnego przeznaczenia v2. Konto magazynu można utworzyć i skonfigurować tak, aby polegało na kluczu szyfrowania konta przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu usługi Azure Resource Manager.

> [!NOTE]
> Tylko magazyn kolejki i tabel można opcjonalnie skonfigurować do szyfrowania danych przy przy przywiązywanie do klucza szyfrowania konta podczas tworzenia konta magazynu. Magazyn obiektów Blob i usługi Azure Files zawsze używają klucza szyfrowania konta do szyfrowania danych.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby użyć programu PowerShell do utworzenia konta magazynu, które opiera się na klucz szyfrowania konta, upewnij się, że zainstalowano moduł programu Azure PowerShell w wersji 3.4.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps).

Następnie utwórz ogólne przeznaczenie konta magazynu w wersji 2, wywołując polecenie [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) z odpowiednimi parametrami:

- Dołącz `-EncryptionKeyTypeForQueue` tę opcję i `Account` ustaw jej wartość, aby używać klucza szyfrowania konta do szyfrowania danych w magazynie kolejek.
- Dołącz `-EncryptionKeyTypeForTable` tę opcję i `Account` ustaw jej wartość, aby używać klucza szyfrowania konta do szyfrowania danych w magazynie tabel.

W poniższym przykładzie pokazano, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2, które jest skonfigurowane do magazynu geograficznego dostępu do odczytu (RA-GRS) i który używa klucza szyfrowania konta do szyfrowania danych dla magazynu kolejki i tabeli. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby użyć interfejsu wiersza polecenia platformy Azure do utworzenia konta magazynu, które opiera się na klucz szyfrowania konta, upewnij się, że zainstalowano platformę Azure CLI w wersji 2.0.80 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Następnie utwórz ogólne przeznaczenie konta magazynu w wersji 2, wywołując [polecenie tworzenia konta magazynu az](/cli/azure/storage/account#az-storage-account-create) z odpowiednimi parametrami:

- Dołącz `--encryption-key-type-for-queue` tę opcję i `Account` ustaw jej wartość, aby używać klucza szyfrowania konta do szyfrowania danych w magazynie kolejek.
- Dołącz `--encryption-key-type-for-table` tę opcję i `Account` ustaw jej wartość, aby używać klucza szyfrowania konta do szyfrowania danych w magazynie tabel.

W poniższym przykładzie pokazano, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2, które jest skonfigurowane do magazynu geograficznego dostępu do odczytu (RA-GRS) i który używa klucza szyfrowania konta do szyfrowania danych dla magazynu kolejki i tabeli. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Szablonu](#tab/template)

Poniższy przykład JSON tworzy ogólne przeznaczenie konta magazynu w wersji 2, który jest skonfigurowany do odczytu dostępu do odczytu magazynu geograficznego nadmiarowego (RA-GRS) i który używa klucza szyfrowania konta do szyfrowania danych dla magazynu kolejki i tabeli. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Po utworzeniu konta, które opiera się na klucz szyfrowania konta, zobacz jeden z następujących artykułów, aby skonfigurować klucze zarządzane przez klienta za pomocą usługi Azure Key Vault:

- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu witryny Azure portal](storage-encryption-keys-portal.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu programu PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Weryfikowanie klucza szyfrowania konta

Aby sprawdzić, czy usługa na koncie magazynu używa klucza szyfrowania konta, należy wywołać polecenie [konta magazynu az](/cli/azure/storage/account#az-storage-account-show) interfejsu wiersza polecenia platformy Azure. To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj `keyType` pola dla każdej usługi we właściwości szyfrowania i sprawdź, czy jest ustawiona na `Account`.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby sprawdzić, czy usługa na koncie magazynu używa klucza szyfrowania konta, należy wywołać polecenie [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj `KeyType` pola dla `Encryption` każdej usługi we właściwości `Account`i sprawdź, czy jest ustawiona na .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić, czy usługa na koncie magazynu używa klucza szyfrowania konta, zadzwoń do polecenia [az storage account.](/cli/azure/storage/account#az-storage-account-show) To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj `keyType` pola dla każdej usługi we właściwości szyfrowania i sprawdź, czy jest ustawiona na `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Szablonu](#tab/template)

Nie dotyczy

---

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](storage-service-encryption.md) 
- [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
