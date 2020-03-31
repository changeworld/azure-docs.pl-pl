---
title: Przekazywanie dużego zestawu danych do usługi Azure Data Lake Storage Gen1 — metody w trybie offline
description: Kopiowanie danych z magazynu obiektów Blob platformy Azure do usługi Azure Data Lake Storage Gen1 za pomocą usługi Import/Eksportowanie
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: aa3eb0bcd9ddd2a094563efe326f7af7e9e8708a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839309"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Użyj usługi importu/eksportu platformy Azure do kopiowania danych w trybie offline do usługi Data Lake Storage Gen1

W tym artykule dowiesz się, jak skopiować ogromne zestawy danych (>200 GB) do usługi Data Lake Storage Gen1 przy użyciu metod kopiowania w trybie offline, takich jak [usługa Azure Import/Export .](../storage/common/storage-import-export-service.md) W szczególności plik używany jako przykład w tym artykule jest 339,420,860,416 bajtów lub około 319 GB na dysku. Nazwijmy ten plik 319GB.tsv.

Usługa importu/eksportu platformy Azure ułatwia bezpieczniejsze przesyłanie dużych ilości danych do magazynu obiektów Blob platformy Azure przez wysyłanie dysków twardych do centrum danych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy mieć następujące właściwości:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu platformy Azure**.
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Przygotowywanie danych

Przed skorzystaniem z usługi Import/Eksportuj należy podzielić plik danych, który ma zostać przeniesiony do kopii o rozmiarze **mniejszym niż 200 GB.** Narzędzie importu nie działa z plikami większymi niż 200 GB. W tym artykule dzielimy plik na fragmenty po 100 GB każdy. Można to zrobić za pomocą [Cygwin](https://cygwin.com/install.html). Cygwin obsługuje polecenia Linuksa. W takim przypadku należy użyć następującego polecenia:

    split -b 100m 319GB.tsv

Operacja podziału tworzy pliki o następujących nazwach.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Przygotowanie dysków z danymi

Postępuj zgodnie z instrukcjami w [użyciu usługi Azure Import/Eksport](../storage/common/storage-import-export-service.md) (w sekcji **Przygotowywanie dysków),** aby przygotować dyski twarde. Oto ogólna sekwencja:

1. Zaopatrzyj się w dysk twardy, który spełnia wymagania, które mają być używane dla usługi Azure Import/Export.
2. Zidentyfikuj konto magazynu platformy Azure, na którym dane zostaną skopiowane po wysłaniu ich do centrum danych platformy Azure.
3. Użyj [narzędzia Azure Import/Export Tool](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), narzędzia wiersza polecenia. Oto przykładowy fragment kodu, który pokazuje, jak korzystać z narzędzia.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Zobacz [korzystanie z usługi Import/Eksportowanie platformy Azure, aby](../storage/common/storage-import-export-service.md) uzyskać więcej przykładowych fragmentów kodu.
4. Poprzednie polecenie tworzy plik dziennika w określonej lokalizacji. Ten plik dziennika służy do tworzenia zadania importu z [witryny Azure portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Tworzenie zadania importu

Teraz można utworzyć zadanie importu przy użyciu instrukcji w [użyciu usługi Azure Import/Eksport](../storage/common/storage-import-export-service.md) (w sekcji **Tworzenie zadania importu).** Dla tego zadania importu, z innymi szczegółami, należy również podać plik dziennika utworzony podczas przygotowywania dysków.

## <a name="physically-ship-the-disks"></a>Fizycznie wysyłaj dyski

Teraz można fizycznie wysłać dyski do centrum danych platformy Azure. Tam dane są kopiowane do obiektów blob usługi Azure Storage podanych podczas tworzenia zadania importu. Ponadto podczas tworzenia zadania, jeśli zdecydujesz się podać informacje o śledzeniu później, możesz teraz wrócić do zadania importu i zaktualizować numer śledzenia.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów blob do usługi Data Lake Storage Gen1

Po podaniu stanu zadania importu pokazuje, że jest ukończony, można sprawdzić, czy dane są dostępne w obiektach blob usługi Azure Storage, które zostały określone. Następnie można użyć różnych metod, aby przenieść te dane z obiektów blob do usługi Azure Data Lake Storage Gen1. Aby uzyskać wszystkie dostępne opcje przesyłania danych, zobacz [Łganie danych do usługi Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

W tej sekcji udostępniamy definicje JSON, których można użyć do utworzenia potoku usługi Azure Data Factory do kopiowania danych. Tych definicji JSON można użyć z [portalu Azure](../data-factory/tutorial-copy-data-portal.md) lub programu [Visual Studio.](../data-factory/tutorial-copy-data-dot-net.md)

### <a name="source-linked-service-azure-storage-blob"></a>Źródło połączone usługi (obiekt blob usługi Azure Storage)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Usługa połączona docelowa (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Zestaw danych wejściowych

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Zestaw danych wyjściowych

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Potok (działanie kopiowania)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Aby uzyskać więcej informacji, zobacz [Przenoszenie danych z obiektu blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory.](../data-factory/connector-azure-data-lake-store.md)

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Odtworzenie plików danych w umiań przechowywania w umiań danych

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zaczęliśmy od pliku o rozmiarze 319 GB i rozbiliśmy go na pliki o mniejszym rozmiarze, aby można było go przenieść za pomocą usługi Azure Import/Export. Teraz, gdy dane znajdują się w usłudze Azure Data Lake Storage Gen1, możemy odtworzyć plik do jego oryginalnego rozmiaru. W tym celu można użyć następujących poleceń cmdlet programu Azure PowerShell.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
