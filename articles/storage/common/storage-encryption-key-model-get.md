---
title: Określanie, który model klucza szyfrowania jest używany dla konta magazynu
titleSuffix: Azure Storage
description: Użyj witryny Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, aby sprawdzić, jak klucze szyfrowania są zarządzane dla konta magazynu. Kluczami może zarządzać firma Microsoft (wartość domyślna) lub klient. Klucze zarządzane przez klienta muszą być przechowywane w usłudze Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409863"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Określanie, który model klucza szyfrowania usługi Azure Storage jest używany dla konta magazynu

Dane na koncie magazynu są automatycznie szyfrowane przez usługę Azure Storage. Szyfrowanie usługi Azure Storage oferuje dwie opcje zarządzania kluczami szyfrowania na poziomie konta magazynu:

- **Klucze zarządzane przez firmę Microsoft.** Domyślnie firma Microsoft zarządza kluczami używanymi do szyfrowania konta magazynu.
- **Klucze zarządzane przez klienta.** Opcjonalnie można wybrać zarządzanie kluczami szyfrowania dla konta magazynu. Klucze zarządzane przez klienta muszą być przechowywane w usłudze Azure Key Vault.

Ponadto można podać klucz szyfrowania na poziomie indywidualnego żądania dla niektórych operacji magazynu obiektów Blob. Gdy klucz szyfrowania jest określony w żądaniu, ten klucz zastępuje klucz szyfrowania, który jest aktywny na koncie magazynu. Aby uzyskać więcej informacji, zobacz [Określanie klucza dostarczonego przez klienta w żądaniu magazynu obiektów Blob](../blobs/storage-blob-customer-provided-key.md).

Aby uzyskać więcej informacji na temat kluczy szyfrowania, zobacz [Szyfrowanie usługi Azure Storage dla danych w spoczynku](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Sprawdź model klucza szyfrowania dla konta magazynu

Aby ustalić, czy konto magazynu używa kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta do szyfrowania, użyj jednej z następujących metod.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

Aby sprawdzić model szyfrowania konta magazynu przy użyciu witryny Azure portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **Szyfrowanie** i zanotuj to ustawienie.

Na poniższej ilustracji przedstawiono konto magazynu szyfrowane przy pomocą kluczy zarządzanych przez firmę Microsoft:

![Wyświetlanie konta zaszyfrowanego za pomocą kluczy zarządzanych przez firmę Microsoft](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Na poniższej ilustracji przedstawiono konto magazynu szyfrowane za pomocą kluczy zarządzanych przez klienta:

![Zrzut ekranu przedstawiający ustawienie klucza szyfrowania w witrynie Azure portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Aby sprawdzić model szyfrowania dla konta magazynu przy użyciu programu PowerShell, należy wywołać polecenie [Get-AzStorageAccount,](/powershell/module/az.storage/get-azstorageaccount) a następnie sprawdzić właściwość **KeySource** dla konta.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Jeśli wartość właściwości **KeySource** `Microsoft.Storage`jest , a następnie konto jest szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Jeśli wartość **właściwości KeySource** `Microsoft.Keyvault`jest , a następnie konto jest szyfrowane za pomocą kluczy zarządzanych przez klienta.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby sprawdzić model szyfrowania dla konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, zadzwoń do [polecenia show konta magazynu az,](/cli/azure/storage/account#az-storage-account-show) a następnie sprawdź właściwość **keySource** dla konta.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Jeśli wartość **właściwości keySource** `Microsoft.Storage`jest , a następnie konto jest szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Jeśli wartość **keySource** właściwość `Microsoft.Keyvault`jest , a następnie konto jest szyfrowane za pomocą kluczy zarządzanych przez klienta.

---

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](storage-service-encryption.md)
- [Zarządzanie szyfrowaniem usługi Azure Storage za pomocą kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault](encryption-customer-managed-keys.md)