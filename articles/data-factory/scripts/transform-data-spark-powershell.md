---
title: Przekształcanie danych w chmurze za pomocą programu PowerShell
description: Ten skrypt programu PowerShell przekształca dane w chmurze, uruchamiając program Spark w klastrze platformy Azure HDInsight Spark.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c09d0532b845472d0ccaac1ad57e3772630bb5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932064"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Skrypt programu PowerShell — przekształcanie danych w chmurze przy użyciu usługi Azure Data Factory

Ten przykładowy skrypt programu PowerShell tworzy potok, który przekształca dane w chmurze, uruchamiając program Spark w klastrze platformy Azure HDInsight Spark. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Konto usługi Azure Storage**. Utwórz skrypt języka Python i plik wejściowy i przekaż je do magazynu platformy Azure. Dane wyjściowe programu platformy Spark są przechowywane na tym koncie magazynu. Klaster platformy Spark na żądanie używa tego samego konta magazynu, jako swojego podstawowego magazynu.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Przekazywanie skryptu w języku Python do konta usługi Blob Storage
1. Utwórz plik języka Python o nazwie **WordCount_Spark.py** z następującą zawartością: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Zamień ** &lt;storageAccountName&gt; ** na nazwę swojego konta usługi Azure Storage. Następnie zapisz plik. 
3. W usłudze Azure Blob Storage utwórz kontener o nazwie **adftutorial**, jeśli nie istnieje. 
4. Utwórz folder o nazwie **spark**.
5. Utwórz podfolder o nazwie **script** w folderze **spark**. 
6. Przekaż plik **WordCount_Spark.py** do podfolderu **script**. 


### <a name="upload-the-input-file"></a>Przekazywanie pliku wejściowego
1. Utwórz plik o nazwie **minecraftstory.txt** zawierający tekst. Program platformy Spark zlicza liczbę słów w tym tekście. 
2. Utwórz podfolder `inputfiles` o `spark` nazwie w folderze kontenera obiektów blob. 
3. Przekaż `minecraftstory.txt` do podfolderu `inputfiles`. 

## <a name="sample-script"></a>Przykładowy skrypt
> [!IMPORTANT]
> Ten skrypt tworzy pliki JSON definiujące jednostki Data Factory (usługa połączona, zestaw danych i potok) na dysku twardym w c:\ Folder.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Tworzy usługę połączony w fabryce danych. Połączona usługa łączy magazyn danych lub obliczeń z fabryką danych. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Tworzy potok w fabryce danych. Potok zawiera co najmniej jedną operację, która wykonuje określoną operację. W tym potoku spark działania przekształca dane przez uruchomienie programu w klastrze platformy Azure HDInsight Spark. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Tworzy przebieg dla potoku. Innymi słowy uruchamia potoku. |
| [Get-AzDataFactoryV2AktypcjaRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Pobiera szczegółowe informacje na temat uruchamiania działania (przebiegu działania) w potoku. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Dodatkowe przykłady skryptów usługi Azure Data Factory PowerShell można znaleźć w [przykładach usługi Azure Data Factory PowerShell.](../samples-powershell.md)
