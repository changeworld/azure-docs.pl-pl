---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — usuwanie kontenerów na podstawie prefiksu | Microsoft Docs
description: Usuwanie kontenerów obiektów blob w usłudze Azure Storage na podstawie prefiksu nazwy kontenera.
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
ms.openlocfilehash: 85ee6505adafab9587f3583cd4c7182efcc43c11
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853735"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Usuwanie kontenerów na podstawie prefiksu nazwy kontenera

Ten skrypt tworzy kilka przykładowych kontenerów w usłudze Azure Blob Storage, a następnie usuwa niektóre z nich na podstawie prefiksu nazwy kontenera.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, pozostałe kontenery i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do usunięcia kontenerów na podstawie prefiksu nazwy kontenera. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto usługi Azure Storage we wskazanej grupie zasobów. |
| [az storage container create](/cli/azure/storage/container#az_storage_container_create) | Tworzy kontener w usłudze Azure Blob Storage. |
| [az storage container list](/cli/azure/storage/container) | Zwraca listę kontenerów na koncie usługi Azure Storage. |
| [az storage container delete](/cli/azure/storage/container#az_storage_container_delete) | Usuwa kontenery z konta usługi Azure Storage. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Storage można znaleźć na stronie [Azure CLI samples for Azure Storage (Przykłady skryptów interfejsu wiersza polecenia platformy Azure dla usługi Azure Storage)](../blobs/storage-samples-blobs-cli.md).
