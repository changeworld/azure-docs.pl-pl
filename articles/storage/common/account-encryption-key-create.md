---
title: Utwórz konto, które obsługuje klucze zarządzane przez klienta dla tabel i kolejek
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć konto magazynu, które obsługuje Konfigurowanie kluczy zarządzanych przez klienta dla tabel i kolejek. Użyj interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager, aby utworzyć konto magazynu, które opiera się na kluczu szyfrowania konta na potrzeby szyfrowania usługi Azure Storage. Następnie można skonfigurować dla konta klucze zarządzane przez klienta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8cf1f8ecb68e31f93c19d93d6ebc4f8ef37724e7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028441"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Utwórz konto, które obsługuje klucze zarządzane przez klienta dla tabel i kolejek

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie usługa queue storage i Table Storage używają klucza, który jest objęty zakresem usługi i jest zarządzany przez firmę Microsoft. Możesz również wybrać użycie kluczy zarządzanych przez klienta do szyfrowania danych z kolejki lub tabeli. Aby używać kluczy zarządzanych przez klienta z kolejkami i tabelami, należy najpierw utworzyć konto magazynu używające klucza szyfrowania, który jest objęty zakresem konta, a nie z usługą. Po utworzeniu konta korzystającego z klucza szyfrowania konta dla danych z kolejki i tabeli można skonfigurować klucze zarządzane przez klienta w Azure Key Vault dla tego konta magazynu.

W tym artykule opisano sposób tworzenia konta magazynu, które opiera się na kluczu, który jest objęty zakresem konta. Po pierwszym utworzeniu konta firma Microsoft używa klucza konta do szyfrowania danych na koncie, a firma Microsoft zarządza kluczem. Następnie można skonfigurować klucze zarządzane przez klienta dla konta, aby skorzystać z tych korzyści, w tym z możliwością udostępnienia własnych kluczy, zaktualizowania wersji klucza, obrócenia kluczy i odwołaniu kontroli dostępu.

## <a name="about-the-feature"></a>Informacje o funkcji

Aby utworzyć konto magazynu, które opiera się na kluczu szyfrowania konta dla usługi Queue i Table Storage, musisz najpierw zarejestrować się, aby korzystać z tej funkcji na platformie Azure. Ze względu na ograniczoną pojemność należy pamiętać, że może upłynąć kilka miesięcy przed zatwierdzeniem żądań dostępu.

Można utworzyć konto magazynu, które opiera się na kluczu szyfrowania konta dla usługi Queue i Table Storage w następujących regionach:

- Wschodnie stany USA
- Południowo-środkowe stany USA
- Zachodnie stany USA 2  

### <a name="register-to-use-the-account-encryption-key"></a>Zarejestruj się, aby użyć klucza szyfrowania konta

Aby zarejestrować się w interfejsie wiersza polecenia platformy Azure, wywołaj polecenie [AZ Feature Register](/cli/azure/feature#az-feature-register) .

Aby zarejestrować się w celu korzystania z klucza szyfrowania konta z usługą queue storage:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Aby zarejestrować się w celu korzystania z klucza szyfrowania konta w usłudze Table Storage:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="check-the-status-of-your-registration"></a>Sprawdź stan rejestracji

Aby sprawdzić stan rejestracji w usłudze queue storage:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForQueues
```

Aby sprawdzić stan rejestracji w usłudze Table Storage:

```azurecli
az feature show --namespace Microsoft.Storage --name AllowAccountEncryptionKeyForTables
```

### <a name="re-register-the-azure-storage-resource-provider"></a>Zarejestruj ponownie dostawcę zasobów usługi Azure Storage

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. Wywołaj polecenie [AZ Provider Register](/cli/azure/provider#az-provider-register) :

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Tworzenie konta korzystającego z klucza szyfrowania konta

Należy skonfigurować nowe konto magazynu do korzystania z klucza szyfrowania konta dla kolejek i tabel w momencie utworzenia konta magazynu. Nie można zmienić zakresu klucza szyfrowania po utworzeniu konta.

Konto magazynu musi być kontem ogólnego przeznaczenia w wersji 2. Można utworzyć konto magazynu i skonfigurować je w taki sposób, aby korzystało z klucza szyfrowania konta przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager.

> [!NOTE]
> Opcjonalnie można skonfigurować tylko magazyn kolejek i tabel, aby szyfrować dane przy użyciu klucza szyfrowania konta podczas tworzenia konta magazynu. Magazyn obiektów blob i Azure Files zawsze używają klucza szyfrowania konta do szyfrowania danych.

### <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby użyć interfejsu wiersza polecenia platformy Azure do utworzenia konta magazynu, które opiera się na kluczu szyfrowania konta, upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.80 lub nowszej. Aby uzyskać więcej informacji, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując polecenie [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) z odpowiednimi parametrami:

- Dołącz opcję `--encryption-key-type-for-queue` i ustaw jej wartość na `Account`, aby użyć klucza szyfrowania konta do szyfrowania danych w usłudze queue storage.
- Dołącz opcję `--encryption-key-type-for-table` i ustaw jej wartość na `Account`, aby użyć klucza szyfrowania konta do szyfrowania danych w usłudze Table Storage.

Poniższy przykład pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane dla usługi LRS, które używa klucza szyfrowania konta do szyfrowania danych zarówno dla kolejki, jak i dla magazynu tabel. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

### <a name="templatetabtemplate"></a>[Szablon](#tab/template)

Poniższy przykład JSON tworzy konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane dla usługi LRS, które korzysta z klucza szyfrowania konta do szyfrowania danych zarówno dla kolejki, jak i dla magazynu tabel. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach ostrych własnymi wartościami:

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
            "name": "[parameters('Standard_LRS')]"
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

Po utworzeniu konta, które opiera się na kluczu szyfrowania konta, zobacz jeden z następujących artykułów, aby skonfigurować klucze zarządzane przez klienta w Azure Key Vault:

- [Skonfiguruj klucze zarządzane przez klienta za pomocą Azure Key Vault przy użyciu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą Azure Key Vault przy użyciu programu PowerShell](storage-encryption-keys-powershell.md)
- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Weryfikowanie klucza szyfrowania konta

Aby sprawdzić, czy usługa na koncie magazynu korzysta z klucza szyfrowania konta, wywołaj polecenie [AZ Storage account](/cli/azure/storage/account#az-storage-account-show) dla interfejsu wiersza polecenia platformy Azure. To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj pola `keyType` dla każdej usługi we właściwości szyfrowania i sprawdź, czy jest ona ustawiona na `Account`.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md) 
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
