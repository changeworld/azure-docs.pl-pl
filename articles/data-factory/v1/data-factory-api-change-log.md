---
title: Fabryka danych — dziennik zmian interfejsu API platformy .NET
description: W tym artykule opisano zmiany podziału, dodatki funkcji, poprawki błędów i tak dalej, w określonej wersji interfejsu API platformy .NET dla usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74979006"
---
# <a name="azure-data-factory---net-api-change-log"></a>Usługa Azure Data Factory — dziennik zmian interfejsu API platformy .NET
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Ten artykuł zawiera informacje o zmianach w usłudze Azure Data Factory SDK w określonej wersji. Najnowsze pakiety NuGet dla usługi Azure Data Factory można znaleźć [tutaj](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Wersja 4.11.0
Dodatki do funkcji:

* Dodano następujące połączone typy usług:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftŁączedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService (Usługa AwsAccessKeyLinkedService)](https://msdn.microsoft.com/library/mt765144.aspx)
* Dodano następujące typy zestawów danych:
  * [Zestaw danych MongoDbCollection](https://msdn.microsoft.com/library/mt765145.aspx)
  * [Zestaw danych AmazonS3](https://msdn.microsoft.com/library/mt765112.aspx)
* Dodano następujące typy źródeł kopiowania:
  * [MongoDbSource (Źródło mongodb)](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Wersja 4.10.0
* Do textformat dodano następujące właściwości opcjonalne:
  * [SkipLineCount (Liczba pomijania)](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [PierwszyWZdań](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Dodano następujące połączone typy usług:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService (SalesforceLinkedService)](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Dodano następujące typy zestawów danych:
  * [Zestaw danych OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Dodano następujące typy źródeł kopiowania:
  * [CassandraŹródło](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Dodaj właściwość [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) do usługi AzureMLBatchExecutionActivity
  * Włączanie przekazywania wielu danych wejściowych usługi sieci Web do eksperymentu usługi Azure Machine Learning

## <a name="version-491"></a>Wersja 4.9.1
### <a name="bug-fix"></a>Poprawka
* Przestarzałe uwierzytelnianie oparte na webapi dla [usługi WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Wersja 4.9.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Dodaj [enablestaging](https://msdn.microsoft.com/library/mt767916.aspx) i [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) właściwości copyactivity. Zobacz [Kopia etapowa,](data-factory-copy-activity-performance.md#staged-copy) aby uzyskać szczegółowe informacje na temat funkcji.

### <a name="bug-fix"></a>Poprawka
* Wprowadzenie przeciążenia [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metody, która przyjmuje [ActivityWindowsByActivityListParameters wystąpienie.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx)
* Mark [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) i [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) jako opcjonalne w CopySink.

## <a name="version-480"></a>Wersja 4.8.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Następujące właściwości opcjonalne zostały dodane do typu działania Kopiowanie, aby umożliwić dostrajanie wydajności kopiowania:
  * [RównoległeCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [Jednostki clouddataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Wersja 4.7.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Dodano nowy typ Formatu magazynu [OrcFormat,](https://msdn.microsoft.com/library/mt723391.aspx) aby skopiować pliki w zoptymalizowanym formacie kolumnowym wiersza (ORC).
* Dodaj [właściwości AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) i PolyBaseSettings do sqldwsink.
  * Umożliwia kopiowanie danych do magazynu danych SQL za pomocą bazy danych PolyBase.

## <a name="version-461"></a>Wersja 4.6.1
### <a name="bug-fixes"></a>Poprawki błędów
* Rozwiązuje żądanie HTTP dotyczące wyświetlania okien aktywności.
  * Usuwa nazwę grupy zasobów i nazwę fabryki danych z ładunku żądania.

## <a name="version-460"></a>Wersja 4.6.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Do [właściwości PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)dodano następujące właściwości:
  * [Tryb potoku](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Zestawy danych](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Do [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)dodano następujące właściwości:
  * [Stan potoku](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Dodano nowy typ [Formatowania magazynu](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) [typu JsonFormat,](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) aby zdefiniować zestawy danych, których dane są w formacie JSON.

## <a name="version-450"></a>Wersja 4.5.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Dodano [operacje listy dla okna aktywności](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Dodano metody pobierania okien aktywności z filtrami opartymi na typach jednostek (czyli fabrykach danych, zestawach danych, potokach i działaniach).
* Dodano następujące połączone typy usług:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Dodano następujące typy zestawów danych:
  * [Zestaw danych ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Dodano następujące typy źródeł kopiowania:     
  * [Źródło sieci Web](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Wersja 4.4.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Następujący typ połączonej usługi został dodany jako źródła danych i pochłaniacze dla działań kopiowania:
  * [Usługa AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Zobacz [Usługi linked service usługi Azure Storage SAS,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) aby uzyskać informacje o pojęciach i przykłady.

## <a name="version-430"></a>Wersja 4.3.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Następujące połączone typy usług zostały dodane jako źródła danych dla działań kopiowania:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Zobacz [Przenoszenie danych z systemu plików HDFS przy użyciu fabryki danych, aby](data-factory-hdfs-connector.md) uzyskać informacje koncepcyjne i przykłady.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Zobacz [Przenoszenie danych z magazynów danych ODBC przy użyciu usługi Azure Data Factory, aby](data-factory-odbc-connector.md) uzyskać informacje koncepcyjne i przykłady.

## <a name="version-420"></a>Wersja 4.2.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Dodano następujący nowy typ działania: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Aby uzyskać szczegółowe informacje na temat tego działania, zobacz [Aktualizowanie modeli usługi Azure ML przy użyciu działania aktualizującego zasoby](data-factory-azure-ml-batch-execution-activity.md).
* Nowa opcjonalna [właściwość updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) została dodana do [klasy AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) i [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) właściwości zostały dodane do [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) klasy.
* Zezwalaj na konfigurację limitów czasu dla wywołań klienta do usługi Data Factory.

## <a name="version-410"></a>Wersja 4.1.0
### <a name="feature-additions"></a>Dodatki do funkcji
* Dodano następujące połączone typy usług:
  * [Usługa AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [Usługa AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Dodano następujące typy działań:
  * [DataLakeAnalyticsUSQLAkktywność](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Dodano następujące typy zestawów danych:
  * [Zestaw danych usługi AzureDataLakeStore](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Dodano następujące typy źródeł i ujścia dla działania kopiowania:
  * [Usługa AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Wersja 4.0.1
### <a name="breaking-changes"></a>Fundamentalne zmiany
Zmieniono nazwę następujących klas. Nowe nazwy były oryginalne nazwy klas przed wydaniem 4.0.0.

| Imię i nazwisko w 4.0.0 | Nazwa w 4.0.1 |
|:--- |:--- |
| Zestaw danych usługi AzureSqlDataWarehouse |[Zestaw danych tabeli tabeli usługi AzureSqlDataWarehouseTable](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| Zestaw danych usługi AzureSql |[Zestaw danych usługi AzureSqlTable](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| Zestaw danych platformy Azure |[Zestaw danych usługi AzureTable](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| Zestaw danych Oracle |[Zestaw danych OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| Zestaw danych relacyjnych |[Zestaw danych tabeli relacyjnej](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| Zestaw danych programu SqlServer |[Zestaw danych SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Wersja 4.0.0
### <a name="breaking-changes"></a>Fundamentalne zmiany
* Następujące klasy/interfejsy zostały zmienione.

| Stara nazwa | Nowa nazwa |
|:--- |:--- |
| Operacji itable |[IDatasetOperacje](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| Właściwości tabeli |[Właściwości zestawu danych](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[Właściwości zestawu danychTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[Zestaw danychCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse (Stawa tabeli) |[Zestaw danychGetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| Lista tabelOdpowiedzialność |[Lista danychOdpowiedzialność](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[Zestaw danychCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **Lista** metody zwracają wyniki sstronicowania teraz. Jeśli odpowiedź zawiera niepustą właściwość **NextLink,** aplikacja kliencka musi kontynuować pobieranie następnej strony, dopóki nie zostaną zwrócone wszystkie strony.  Oto przykład:

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
* **Interfejs** API potoku listy zwraca tylko podsumowanie potoku zamiast pełnych szczegółów. Na przykład działania w podsumowaniu potoku zawierają tylko nazwę i typ.

### <a name="feature-additions"></a>Dodatki do funkcji
* [Klasa SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) obsługuje dwie nowe właściwości, **SliceIdentifierColumnName** i **SqlWriterCleanupScript**, do obsługi idempotentnej kopii do usługi Azure SQL Data Warehouse. Zobacz artykuł [usługi Azure SQL Data Warehouse, aby](data-factory-azure-sql-data-warehouse-connector.md) uzyskać szczegółowe informacje na temat tych właściwości.
* Obecnie obsługujemy uruchamianie procedury składowanej w witrynie Azure SQL Database i źródłach usługi Azure SQL Data Warehouse w ramach działania kopiowania. [Klasy SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) i [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) mają następujące właściwości: **SqlReaderStoredProcedureName** i **StoredProcedureParameters**. Szczegółowe informacje na temat tych właściwości można znaleźć w artykułach [usługi Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) i Azure SQL Data [Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) Azure.com.  
