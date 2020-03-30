---
title: Kopiowanie danych w chmurze przy użyciu programu PowerShell
description: Ten skrypt programu PowerShell kopiuje dane z jednej lokalizacji w magazynie obiektów Blob platformy Azure do innej lokalizacji w tym samym magazynie obiektów Blob.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 2e289f30ef1c1883c38884eb563a41bccc841329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462655"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Tworzenie potoku fabryki danych za pomocą programu PowerShell do kopiowania danych w chmurze

Ten przykładowy skrypt programu PowerShell tworzy potok w usłudze Azure Data Factory, który kopiuje dane z jednej lokalizacji do innej lokalizacji w magazynie obiektów Blob platformy Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany zarówno jako magazyn **źródła**, jak i **ujścia** danych. Jeśli nie masz konta magazynu platformy Azure, zobacz [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) podczas tworzenia konta magazynu. 
* Utwórz **kontener obiektów blob** w usłudze Blob Storage, utwórz **folder** wejściowy w kontenerze i przekaż niektóre pliki do folderu. Narzędzia, takie jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), umożliwiają łączenie z usługą Azure Blob Storage, tworzenie kontenera obiektów blob, przekazywanie pliku wejściowego i weryfikację pliku wyjściowego.

## <a name="sample-script"></a>Przykładowy skrypt

> [!IMPORTANT]
> Ten skrypt tworzy pliki JSON definiujące jednostki Data Factory (usługa połączona, zestaw danych i potok) na dysku twardym w c:\ Folder.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po uruchomieniu przykładowego skryptu można użyć następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Aby usunąć fabrykę danych z grupy zasobów, uruchom następujące polecenie:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Zestaw-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Tworzenie fabryki danych. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Tworzy usługę połączony w fabryce danych. Połączona usługa łączy magazyn danych lub obliczeń z fabryką danych. |
| [Zestaw danych Zestawu AzDataFactoryV2](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Tworzy zestaw danych w fabryce danych. Zestaw danych reprezentuje dane wejściowe/wyjściowe dla działania w potoku. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera co najmniej jedną operację, która wykonuje określoną operację. W tym potoku działanie kopiowania kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Tworzy przebieg dla potoku. Innymi słowy uruchamia potoku. |
| [Get-AzDataFactoryV2AktypcjaRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Pobiera szczegółowe informacje na temat uruchamiania działania (przebiegu działania) w potoku.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów usługi Azure Data Factory PowerShell można znaleźć w [przykładach usługi Azure Data Factory PowerShell.](../samples-powershell.md)
