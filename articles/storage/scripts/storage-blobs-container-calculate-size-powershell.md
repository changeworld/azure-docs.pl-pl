---
title: Skrypt programu PowerShell Azure przykładowe — obliczania rozmiaru kontenera obiektu blob | Dokumentacja firmy Microsoft
description: Obliczania rozmiaru kontenera w magazynie obiektów Blob Azure przez sumowanie rozmiar każdego z jego obiektów blob.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2017
ms.locfileid: "24814579"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Obliczania rozmiaru kontenera magazynu obiektów Blob

Ten skrypt oblicza rozmiar kontenera w magazynie obiektów Blob Azure przez sumowanie rozmiar obiektów blob w kontenerze.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ten skrypt programu PowerShell zawiera szacowany rozmiar kontenera i nie powinna być używana związanych z rozliczeniami obliczeń. Aby uzyskać skrypt, który oblicza rozmiar kontenera do celów rozliczeń, zobacz [obliczania rozmiaru kontenera magazynu obiektów Blob na potrzeby rozliczeń](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, kontenera i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń do obliczania rozmiaru kontenera magazynu obiektów Blob. Każdy element w tabeli łącza do dokumentacji specyficzne dla polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Pobiera określonego konta magazynu lub wszystkich kont magazynu w grupie zasobów lub subskrypcji. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Wyświetla listę obiektów blob w kontenerze. ||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać skrypt, który oblicza rozmiar kontenera do celów rozliczeń, zobacz [obliczania rozmiaru kontenera magazynu obiektów Blob na potrzeby rozliczeń](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Aby uzyskać więcej informacji dotyczących modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/overview).

Przykłady skryptów PowerShell dodatkowego miejsca do magazynowania znajdują się w [przykłady środowiska PowerShell dla usługi Azure Storage](../blobs/storage-samples-blobs-powershell.md).
