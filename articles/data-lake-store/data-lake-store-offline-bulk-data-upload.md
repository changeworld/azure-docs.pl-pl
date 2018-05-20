---
title: Przekaż dużych ilości danych do usługi Data Lake Store za pomocą metod w trybie offline | Dokumentacja firmy Microsoft
description: Użyj narzędzia AdlCopy można skopiować danych z obiektów blob magazynu Azure do usługi Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 03d8cc4440d7adcb947e7c150a565e51b6401e92
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Użyj usługi Import/Eksport Azure dla trybu offline kopię danych do usługi Data Lake Store
W tym artykule omówiono sposób kopiowania dużych zestawów danych (> 200 GB) do usługi Azure Data Lake Store za pomocą metody kopiowania w trybie offline, tak samo, jak [usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md). W szczególności plik używany na przykład w tym artykule jest 339,420,860,416 bajtów lub około 319 GB na dysku. Umożliwia wywołanie 319GB.tsv tego pliku.

Usługi Import/Eksport Azure umożliwia bardziej bezpieczny sposób transfer dużych ilości danych do magazynu obiektów Blob platformy Azure poprzez wysyłanie dysków twardych do centrum danych Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu platformy Azure**.
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Przygotowywanie danych

Przed rozpoczęciem korzystania z usługi Import/eksport, Podziel ma zostać przesłany plik danych **do kopie, które są mniej niż 200 GB** rozmiar. Narzędzie importu nie działa z pliki większe niż 200 GB. W tym samouczku możemy podzielić go na fragmenty 100 GB. Można to zrobić za pomocą [programów Cygwin](https://cygwin.com/install.html). Programów Cygwin obsługuje polecenia systemu Linux. W takim przypadku należy użyć następującego polecenia:

    split -b 100m 319GB.tsv

Operacja podziału tworzy pliki z następującymi nazwami.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Przygotowanie dysków z danymi
Postępuj zgodnie z instrukcjami [za pomocą usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md) (w obszarze **przygotowanie dysków** sekcji) do przygotowania dyskach twardych. Poniżej przedstawiono ogólną sekwencję:

1. Uzyskaj dysku twardego, który spełnia wymagania do użycia dla usługi Import/Eksport Azure.
2. Określ konto magazynu Azure, w którym dane zostaną skopiowane po wysłaniu go do centrum danych Azure.
3. Użyj [narzędzie importu/eksportu Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), narzędzie wiersza polecenia. Oto przykładowe fragment kodu, który pokazuje, jak korzystać z narzędzia.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Zobacz [za pomocą usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md) dla więcej przykładowe fragmenty kodu.
4. Poprzednie polecenie tworzy plik dziennika w określonej lokalizacji. Ten plik dziennika umożliwia utworzenie zadania importu z [portalu Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Utwórz zadania importu
Teraz można utworzyć zadania importu zgodnie z instrukcjami podanymi w [za pomocą usługi Import/Eksport Azure](../storage/common/storage-import-export-service.md) (w obszarze **Tworzenie zadania importu** sekcji). W przypadku tego zadania importu z innych szczegółów zapewnić pliku dziennika utworzonego podczas przygotowywania dysków.

## <a name="physically-ship-the-disks"></a>Fizycznie wysłać dysków
Teraz można fizycznie wysłać dysków do centrum danych Azure. Danych jest kopiowana za pośrednictwem obiektów blob magazynu Azure, dostępne podczas tworzenia zadania importu. Ponadto podczas tworzenia zadania, jeśli użytkownik postanowił Podaj informacje o śledzeniu później, należy można teraz wróć do zadania importu i zaktualizować numer.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Kopiowanie danych z obiektów blob magazynu Azure do usługi Azure Data Lake Store
Po stan zadania importu pokazuje, że zostało ukończone, można sprawdzić, czy dane są dostępne w obiektach blob magazynu Azure, ma określone. Można następnie użyć różnych metod Aby przenieść dane z obiektów blob do usługi Azure Data Lake Store. Wszystkie dostępne opcje dotyczące przekazywania danych, zobacz [wprowadzania danych do usługi Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

W tej sekcji możemy udostępnić definicji JSON, których można utworzyć potok fabryki danych Azure kopiowania danych. Korzystając z tych definicji JSON z [portalu Azure](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), lub [programu Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Źródło połączone usługi (obiektu blob magazynu Azure)
````
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
````

### <a name="target-linked-service-azure-data-lake-store"></a>Docelowe połączone usługi (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Wejściowy zestaw danych
````
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
````
### <a name="output-data-set"></a>Zestaw danych wyjściowych
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Potok (działanie kopiowania)
````
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
````
Aby uzyskać więcej informacji, zobacz [przenieść dane z obiektu blob magazynu Azure do usługi Azure Data Lake Store przy użyciu fabryki danych Azure](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Rekonstrukcji pliki danych w usłudze Azure Data Lake Store
Możemy korzystać z pliku, który został 319 GB i spowodowało przerwanie go w dół na pliki mają mniejszy rozmiar, dzięki czemu można dokonać transferu za pomocą usługi Import/Eksport Azure. Teraz, dane są w usłudze Azure Data Lake Store, możemy rekonstrukcji oryginalnego rozmiaru pliku. Aby to zrobić, można użyć następujących cmldts programu Azure PowerShell.

````
# Login to our account
Connect-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext -SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
````

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
