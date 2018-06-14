---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — rotowanie kluczy dostępu do konta magazynu | Microsoft Docs
description: Tworzenie konta usługi Azure Storage oraz pobieranie i rotowanie kluczy dostępu do konta.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 160509a5a82b71b281d57d97e103bb4190605b7c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847815"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Tworzenie konta magazynu i rotowanie kluczy dostępu do konta

Ten skrypt tworzy konto usługi Azure Storage, wyświetla klucze dostępu do nowego konta magazynu, a następnie odnawia (rotuje) te klucze.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, konto magazynu oraz wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do utworzenia konta magazynu oraz pobrania i rotowania kluczy dostępu do tego konta. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto usługi Azure Storage we wskazanej grupie zasobów. |
| [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Wyświetla klucze dostępu do podanego konta magazynu. |
| [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) | Ponownie generuje podstawowy lub pomocniczy klucz dostępu do konta. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Storage można znaleźć na stronie [Azure CLI samples for Azure Blob Storage (Przykłady skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Blob Storage)](../blobs/storage-samples-blobs-cli.md).
