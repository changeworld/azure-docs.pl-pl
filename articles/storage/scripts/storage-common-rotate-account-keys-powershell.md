---
title: Przykładowy skrypt programu Azure PowerShell — rotowanie kluczy dostępu do konta magazynu | Microsoft Docs
description: Tworzenie konta usługi Azure Storage oraz pobieranie i rotowanie jednego z kluczy dostępu do konta.
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
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 0aae733f94ab4a217ca41ca3de6df2e5da47df50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196276"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Tworzenie konta magazynu i rotowanie kluczy dostępu do konta

Ten skrypt tworzy konto usługi Azure Storage, wyświetla podstawowy klucz dostępu do nowego konta magazynu, a następnie odnawia (rotuje) ten klucz.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, konto magazynu oraz wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do utworzenia konta magazynu oraz pobrania i rotowania jednego z kluczy dostępu do tego konta. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Pobiera wszystkie lokalizacje oraz obsługiwanych dostawców zasobów dla każdej z nich. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów platformy Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Tworzy konto magazynu. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Pobiera klucze dostępu dla konta usługi Azure Storage. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Ponownie generuje klucz dostępu do konta usługi Azure Storage. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi Storage można znaleźć na stronie [PowerShell samples for Azure Blob storage (Przykładowe skrypty programu PowerShell dla usługi Azure Blob Storage)](../blobs/storage-samples-blobs-powershell.md).
