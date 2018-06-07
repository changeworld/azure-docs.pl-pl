---
title: Fabryka danych — dziennik zmian interfejsu API platformy .NET | Dokumentacja firmy Microsoft
description: W tym artykule opisano istotne zmiany, funkcje, poprawki itp... w określonej wersji interfejsu API platformy .NET dla fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c5f47379072f4e5d15ffd96c5e45a23d10fff187
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620252"
---
# <a name="azure-data-factory---net-api-change-log"></a>Fabryka danych Azure — dziennik zmian interfejs API .NET
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). 

Ten artykuł zawiera informacje o zmianach wprowadzonych do zestawu SDK usługi Azure Data Factory w określonej wersji. Fabryka danych Azure można znaleźć najnowszy pakiet NuGet [tutaj](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Wersja 4.11.0
Funkcja dodatków:

* Dodano następujące typy połączonej usługi:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Dodano następujące typy zestawu danych:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Dodano następujące typy kopii w źródłowej:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Wersja 4.10.0
* Dodano następujące właściwości opcjonalne TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Dodano następujące typy połączonej usługi:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Dodano następujące typy zestawu danych:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Dodano następujące typy kopii w źródłowej:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Dodaj [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) właściwości AzureMLBatchExecutionActivity
  * Włącz przekazywanie wielu sieci web usługi wejść eksperymentu uczenia maszynowego Azure

## <a name="version-491"></a>Wersja 4.9.1
### <a name="bug-fix"></a>Poprawka błędu
* Zastąpić uwierzytelniania opartego na WebApi dla [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Wersja 4.9.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Dodaj [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) i [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) właściwości do elementu CopyActivity. Zobacz [przemieszczane kopiowania](data-factory-copy-activity-performance.md#staged-copy) szczegółowe informacje dotyczące funkcji.

### <a name="bug-fix"></a>Poprawka błędu
* Wprowadzenie przeciążenia [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metodę, która przyjmuje [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) wystąpienia.
* Oznacz [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) i [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) jako opcjonalne w CopySink.

## <a name="version-480"></a>Wersja 4.8.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Następujące właściwości opcjonalne zostały dodane do kopii typ działania umożliwiające dostrojenie wydajności kopiowania:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Wersja 4.7.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Dodano nowy typ StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) typu, aby skopiować pliki w formacie (ORC) kolumnowym zoptymalizowane wiersza.
* Dodaj [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) i właściwości usługi do SqlDWSink.
  * Umożliwia korzystanie z programu PolyBase, aby skopiować dane do usługi SQL Data Warehouse.

## <a name="version-461"></a>Wersji 4.6.1
### <a name="bug-fixes"></a>Poprawki błędów
* Rozwiązuje żądania HTTP do wyświetlania listy działania systemu windows.
  * Usuwa Nazwa grupy zasobów i nazwa fabryki danych ładunku żądania.

## <a name="version-460"></a>Wersja 4.6.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Dodano następujące właściwości [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [pipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Zestawy danych](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Dodano następujące właściwości [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Dodano nowe [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typu [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) typu do definiowania zestawów danych, którego dane są w formacie JSON.

## <a name="version-450"></a>Wersja 4.5.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Dodaje [listy operacji dla działania okna](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Dodano metody do pobierania okien działania z filtrami na podstawie typów jednostki (oznacza to, fabryk danych, zestawy danych, potoki i działań).
* Dodano następujące typy połączonej usługi:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Dodano następujące typy zestawu danych:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Dodano następujące typy kopii w źródłowej:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Wersja 4.4.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Następujący typ połączonej usługi został dodany jako źródła danych i sink dla działania kopiowania:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Zobacz [połączonej usługi SAS magazynu Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) informacje koncepcyjne i przykłady.

## <a name="version-430"></a>Wersja 4.3.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Następujące był typu połączonej usługi został dodany jako źródła danych dla działania kopiowania:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Zobacz [przenoszenia danych z systemu plików HDFS przy użyciu fabryki danych](data-factory-hdfs-connector.md) informacje koncepcyjne i przykłady.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Zobacz [przenoszenia danych z ODBC magazynów przy użyciu fabryki danych Azure](data-factory-odbc-connector.md) informacje koncepcyjne i przykłady.

## <a name="version-420"></a>Wersja 4.2.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Dodano następujące nowego typu działania: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Aby uzyskać szczegółowe informacje o działaniach, zobacz [modeli aktualizowanie uczenie Maszynowe Azure przy użyciu działanie aktualizacji zasobu](data-factory-azure-ml-batch-execution-activity.md).
* Nowe właściwości opcjonalne [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) został dodany do [AzureMLLinkedService klasy](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) i [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) właściwości zostały dodane do [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) klasy.
* Umożliwiają Konfigurowanie limitów czasu dla połączeń klienta do usługi fabryki danych.

## <a name="version-410"></a>Wersja 4.1.0
### <a name="feature-additions"></a>Dodawanie funkcji
* Dodano następujące typy połączonej usługi:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Dodano następujące typy działań:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Dodano następujące typy zestawu danych:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Dodano następujące typy źródłowy i odbiorczy dla działania kopiowania:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Wersja 4.0.1
### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
Zmieniono następujące klasy. Nazwy nowych sprzed oryginalnej nazwy klasy 4.0.0 wersji.

| Nazwa w 4.0.0 | Nazwa w 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Wersja 4.0.0
### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Zmieniono klas/interfejsów.

| Stara nazwa | Nowa nazwa |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Zestaw danych](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **Listy** metody zwracają teraz wyników stronicowania. Jeśli odpowiedź zawiera niepusty **NextLink** właściwość, aplikacja kliencka musi kontynuować pobieranie następnej strony, dopóki wszystkie strony są zwracane.  Oto przykład:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **Lista** potoku interfejsu API zwraca podsumowanie potoku zamiast szczegółowych informacji. Na przykład działania w potoku podsumowanie zawierać tylko nazwę i typ.

### <a name="feature-additions"></a>Dodawanie funkcji
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) klasa obsługuje dwie nowe właściwości **SliceIdentifierColumnName** i **SqlWriterCleanupScript**, do obsługi kopii idempotentności danych SQL Azure Magazyn. Zobacz [magazyn danych SQL Azure](data-factory-azure-sql-data-warehouse-connector.md) artykułu, aby uzyskać więcej informacji o tych właściwościach.
* Obsługujemy teraz uruchamiania procedury składowanej źródeł bazy danych SQL Azure i magazyn danych SQL Azure w ramach działania kopiowania. [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) i [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) klasy mają następujące właściwości: **SqlReaderStoredProcedureName** i **StoredProcedureParameters**. Zobacz [bazy danych SQL Azure](data-factory-azure-sql-connector.md#sqlsource) i [magazyn danych SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) artykuły w witrynie Azure.com, aby uzyskać więcej informacji o tych właściwościach.  
