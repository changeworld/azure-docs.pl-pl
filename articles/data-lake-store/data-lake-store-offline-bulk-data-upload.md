---
title: Przekazywanie dużych ilości danych do usługi Azure Data Lake Storage Gen1 przy użyciu metod w trybie offline | Dokumentacja firmy Microsoft
description: Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1 za pomocą narzędzia AdlCopy
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4a8126d658f227d9eed372cd51cf06f8f12c99f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885529"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Usługa Azure Import/Export kopię danych do usługi Azure Data Lake Storage Gen1 w trybie offline
W tym artykule dowiesz się, jak skopiować ogromnych zestawów danych (> 200 GB) do usługi Azure Data Lake magazynu Gen1 przy użyciu metod kopii w trybie offline, takie jak [usługa Azure Import/Export](../storage/common/storage-import-export-service.md). Ściślej mówiąc plik, który został użyty w przykładzie w tym artykule jest 339,420,860,416 bajtów lub około 319 GB na dysku. Nadajmy 319GB.tsv tego pliku.

Usługa Azure Import/Export ułatwia bardziej bezpieczne przesyłanie dużych ilości danych do magazynu obiektów Blob platformy Azure przez wysyłanie dysków twardych do centrum danych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem wykonywania tej procedury, musisz mieć następujące czynności:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure storage**.
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Przygotowywanie danych

Przed rozpoczęciem korzystania z usługi Import/Export, Podziel ma zostać przesłany plik danych **do kopii, które są mniej niż 200 GB** rozmiar. Narzędzie do importowania nie działa z plikami większy niż 200 GB. W tym samouczku plik możemy podzielony na fragmenty 100 GB. Można to zrobić za pomocą [Cygwin](https://cygwin.com/install.html). Cygwin obsługuje polecenia systemu Linux. W tym przypadku użyj następującego polecenia:

    split -b 100m 319GB.tsv

Operacja dzielenia tworzy pliki z następującymi nazwami.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Przygotowywanie dysków z danymi
Postępuj zgodnie z instrukcjami w [przy użyciu usługi Azure Import/Export](../storage/common/storage-import-export-service.md) (w obszarze **przygotowywanie dysków** sekcji) do przygotowania dysków twardych. Poniżej przedstawiono ogólną sekwencję:

1. Pozyskiwanie dysku twardego, który spełnia wymagania, które ma być używany dla usługi Azure Import/Export.
2. Określ konto magazynu platformy Azure, w którym dane zostaną skopiowane po wysłaniu go do centrum danych platformy Azure.
3. Użyj [narzędzie importu/eksportu platformy Azure](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), narzędzie wiersza polecenia. Oto przykład fragment kodu, który pokazuje, jak korzystać z narzędzia.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Zobacz [przy użyciu usługi Azure Import/Export](../storage/common/storage-import-export-service.md) dla więcej przykładowych fragmentów kodu.
4. Poprzednie polecenie tworzy plik dziennika w określonej lokalizacji. Ten plik dziennika umożliwia tworzenie zadania importu z [witryny Azure portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Tworzenie zadania importu
Możesz teraz utworzyć zadanie importu, zgodnie z instrukcjami podanymi w [przy użyciu usługi Azure Import/Export](../storage/common/storage-import-export-service.md) (w obszarze **Tworzenie zadania importu** sekcji). Dla tego zadania importu z innymi szczegółami oferują również plik dziennika utworzony podczas przygotowywania dysków.

## <a name="physically-ship-the-disks"></a>Fizycznie dostarczaj dyski
Teraz można fizycznie dostarczaj dyski do centrum danych platformy Azure. Istnieje dane są kopiowane przez obiekty BLOB usługi Azure Storage, podane podczas tworzenia zadania importu. Ponadto podczas tworzenia zadania, jeśli została wybrana, aby zapewnić informacje śledzenia później, możesz można teraz wróć do zadania importu i zaktualizuj numer śledzenia.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1
Gdy stan zadania importu zostanie wyświetlona jego zakończenia, można sprawdzić, czy dane są dostępne w obiektach blob usługi Azure Storage, który ma określony. Można następnie użyć różnych metod Aby przenieść dane z obiektów blob do usługi Azure Data Lake Storage Gen1. Aby uzyskać wszystkie dostępne opcje dotyczące przekazywania danych, zobacz [dane wprowadzane do Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

W tej sekcji firma Microsoft udostępnia użytkownikowi przy użyciu definicji JSON, które można użyć w celu utworzenia potoku usługi Azure Data Factory do kopiowania danych. Możesz użyć tych definicji JSON z [witryny Azure portal](../data-factory/tutorial-copy-data-portal.md) lub [programu Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Źródło połączone usługi (Azure Storage blob)
```
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

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>Docelowa usługa połączona (Azure Data Lake Storage Gen1)
```
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
### <a name="input-data-set"></a>Wejściowy zestaw danych
```
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
### <a name="output-data-set"></a>Wyjściowy zestaw danych
```
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
```
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
Aby uzyskać więcej informacji, zobacz [przenieść dane z usługi Azure Storage blob do usługi Azure Data Lake Storage Gen1 za pośrednictwem usługi Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Odtworzenie plików danych w usłudze Azure Data Lake magazynu Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Rozpoczęliśmy z plikiem, które zostały 319 GB, a przerwało je w dół pliki mniejszego rozmiaru, dzięki czemu mogą być przesyłane za pomocą usługi Azure Import/Export. Teraz, czy dane znajdują się w usłudze Azure Data Lake magazynu Gen1, możemy odtworzyć plik oryginalny rozmiar. Aby to zrobić, można użyć następujących poleceń cmdlet programu Azure PowerShell.

```
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

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
