---
title: Fabryki danych — dziennik zmian interfejsu API .NET | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmiany powodujące niezgodność, funkcje, poprawki błędów itp... w określonej wersji interfejsu API platformy .NET dla usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 863f3500c84eeab1c3dac19141cd334fc6961694
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567252"
---
# <a name="azure-data-factory---net-api-change-log"></a>Usługa Azure Data Factory — dziennik zmian interfejsu API platformy .NET
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Ten artykuł zawiera informacje o zmianach wprowadzonych do zestawu SDK usługi Azure Data Factory w określonej wersji. Dla usługi Azure Data Factory można znaleźć najnowszego pakietu NuGet [tutaj](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Wersja 4.11.0
Dodatkowe funkcje:

* Dodano następujących typów połączonych usług:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Dodano następujące typy zestawu danych:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Dodano następujące typy źródła kopii:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Wersja 4.10.0
* Następujące opcjonalne właściwości zostały dodane do formatu TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Dodano następujących typów połączonych usług:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Dodano następujące typy zestawu danych:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Dodano następujące typy źródła kopii:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Dodaj [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) właściwość działanie AzureMLBatchExecutionActivity
  * Włącz przekazywanie wielu sieci web dane wejściowe usługi eksperymentu usługi Azure Machine Learning

## <a name="version-491"></a>Wersja 4.9.1
### <a name="bug-fix"></a>Naprawienie usterki
* Wycofana uwierzytelniania opartego na interfejsie API dla [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Wersja 4.9.0
### <a name="feature-additions"></a>Funkcje
* Dodaj [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) i [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) właściwości CopyActivity. Zobacz [kopiowania etapowego](data-factory-copy-activity-performance.md#staged-copy) szczegółowe informacje na temat tej funkcji.

### <a name="bug-fix"></a>Naprawienie usterki
* Wprowadzenie przeciążenia [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metody, która przyjmuje [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) wystąpienia.
* Oznacz [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) i [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) jako opcjonalny w CopySink.

## <a name="version-480"></a>Wersja 4.8.0
### <a name="feature-additions"></a>Funkcje
* Następujące opcjonalne właściwości zostały dodane do typu działania kopiowania można włączyć dostrajania wydajności kopiowania:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Wersja 4.7.0
### <a name="feature-additions"></a>Funkcje
* Dodano nowy typ StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) typu, aby skopiować pliki w formacie (ORC) kolumnowym zoptymalizowane wiersza.
* Dodaj [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) i właściwości usługi, które mają SqlDWSink.
  * Umożliwia korzystanie z technologii PolyBase, aby skopiować dane do usługi SQL Data Warehouse.

## <a name="version-461"></a>W wersji 4.6.1
### <a name="bug-fixes"></a>Poprawki błędów
* Rozwiązuje żądania HTTP do wyświetlania listy okien działania.
  * Usuwa nazwę grupy zasobów i nazwy fabryki danych z ładunku żądania.

## <a name="version-460"></a>Wersja 4.6.0
### <a name="feature-additions"></a>Funkcje
* Następujące właściwości zostały dodane do [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [pipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Zestawy danych](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Następujące właściwości zostały dodane do [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Dodano nowe [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typu [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) typu do zdefiniowania zestawów danych, których dane są w formacie JSON.

## <a name="version-450"></a>Wersja 4.5.0
### <a name="feature-additions"></a>Funkcje
* Dodano [listę operacji dla okna działania](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Dodano metody do pobierania okien działania za pomocą filtrów na podstawie typów jednostki (oznacza to, fabryki danych, zestawy danych, potoki i działania).
* Dodano następujących typów połączonych usług:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Dodano następujące typy zestawu danych:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Dodano następujące typy źródła kopii:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Wersję 4.4.0
### <a name="feature-additions"></a>Funkcje
* Następujący typ połączonej usługi został dodany jako źródła danych i sink dla działania kopiowania:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Zobacz [połączoną usługę Azure Storage sygnatury dostępu Współdzielonego](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) koncepcyjne informacje i przykłady.

## <a name="version-430"></a>W wersji 4.3.0
### <a name="feature-additions"></a>Funkcje
* Następujące haven typy połączona usługa została dodana jako źródła danych dla działania kopiowania:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Zobacz [przenoszenie danych z systemu plików HDFS usługa Data Factory](data-factory-hdfs-connector.md) koncepcyjne informacje i przykłady.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Zobacz [Przenieś magazyny danych ODBC z danych za pomocą usługi Azure Data Factory](data-factory-odbc-connector.md) koncepcyjne informacje i przykłady.

## <a name="version-420"></a>Wersja 4.2.0
### <a name="feature-additions"></a>Funkcje
* Dodano następujące nowe typ działania: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Aby uzyskać szczegółowe informacje dotyczące działania, zobacz [modeli usługi uczenie Maszynowe Azure aktualizowanie za pomocą działania aktualizowania zasobów](data-factory-azure-ml-batch-execution-activity.md).
* Nowa właściwość Opcjonalna [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) została dodana do [klasy elementu AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) i [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) właściwości zostały dodane do [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) klasy.
* Umożliwia skonfigurowanie przekroczeń limitu czasu dla połączeń klienta do usługi Data Factory.

## <a name="version-410"></a>Wersja 4.1.0
### <a name="feature-additions"></a>Funkcje
* Dodano następujących typów połączonych usług:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Dodano następujące typy działań:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Dodano następujące typy zestawu danych:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Dodano następujące typy źródła i ujścia dla działania kopiowania:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Wersja 4.0.1
### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
Zmieniono następujące klasy. Nowe nazwy sprzed oryginalne nazwy klas wersji 4.0.0.

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
* Zmieniono nazwy klas/interfejsów.

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

* **Listy** metod teraz zwracają stronicowane wyniki. Jeśli odpowiedź zawiera niepusty **NextLink** właściwości, aplikacja kliencka musi kontynuować pobieranie następnej strony, dopóki nie zostaną zwrócone wszystkie strony.  Oto przykład:

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
* **Lista** potoku interfejsu API zwraca podsumowanie potoku, a nie pełne szczegóły. Na przykład działania w potoku podsumowanie zawierać tylko nazwy i typu.

### <a name="feature-additions"></a>Funkcje
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) klasa obsługuje dwie nowe właściwości, **SliceIdentifierColumnName** i **SqlWriterCleanupScript**, które obsługują idempotentne kopii danych SQL Azure Magazyn. Zobacz [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) artykuł, aby uzyskać szczegółowe informacje o tych właściwościach.
* Jest teraz obsługiwana procedury składowanej wykonanego względem źródeł, Azure SQL Database i Azure SQL Data Warehouse w ramach działania kopiowania. [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) i [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) klasy mają następujące właściwości: **SqlReaderStoredProcedureName** i **StoredProcedureParameters**. Zobacz [usługi Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) i [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) artykułów w witrynie Azure.com, aby uzyskać szczegółowe informacje o tych właściwościach.  
