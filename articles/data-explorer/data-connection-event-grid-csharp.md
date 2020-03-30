---
title: 'Tworzenie połączenia danych siatki zdarzeń dla Eksploratora danych platformy Azure przy użyciu języka C #'
description: W tym artykule dowiesz się, jak utworzyć połączenie danych usługi Event Grid dla Usługi Azure Data Explorer przy użyciu języka C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255076"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Tworzenie połączenia danych siatki zdarzeń dla Eksploratora danych platformy Azure przy użyciu języka C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-event-grid-resource-manager.md)


Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer oferuje pozyskiwania (ładowanie danych) z centrów zdarzeń, usługi IoT Hubs i obiektów blob zapisanych w kontenerach obiektów blob. W tym artykule utworzysz połączenie danych usługi Event Grid dla Usługi Azure Data Explorer przy użyciu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać **bezpłatną** [wersję programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Tworzenie [klastra i bazy danych](create-cluster-database-csharp.md)
* Tworzenie [mapowania tabel i kolumn](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ustawianie [zasad bazy danych i tabel](database-table-policies-csharp.md) (opcjonalnie)
* Utwórz [konto magazynu z subskrypcją usługi Event Grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Dodawanie połączenia danych z siatką zdarzeń

W poniższym przykładzie pokazano, jak programowo dodać połączenie danych usługi Event Grid. Zobacz [tworzenie połączenia danych usługi Event Grid w Eksploratorze danych platformy Azure](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) w celu dodania połączenia danych w usłudze Event Grid przy użyciu witryny Azure portal.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| identyfikator dzierżawy | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| clientSecret | *xxxxxxxxxxxxxx* | Klucz tajny klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie. |
| resourceGroupName | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| clusterName | *mykustocluster* | Nazwa klastra.|
| Databasename | *mykustodatabase* | Nazwa docelowej bazy danych w klastrze.|
| dataConnectionName | *połączenie myeventhub* | Żądana nazwa połączenia danych.|
| tableName | *StormEvents (Burza)* | Nazwa tabeli docelowej w docelowej bazie danych.|
| nazwa mapowaniaRuleName | *StormEvents_CSV_Mapping* | Nazwa mapowania kolumn powiązana z tabelą docelową.|
| Dataformat | *Csv* | Format danych wiadomości.|
| identyfikator eventHubResourceId | *Identyfikator zasobu* | Identyfikator zasobu centrum zdarzeń, w którym siatka zdarzeń jest skonfigurowana do wysyłania zdarzeń. |
| storageAccountResourceId | *Identyfikator zasobu* | Identyfikator zasobu konta magazynu, który przechowuje dane do pozyskiwania. |
| grupa konsumentów | *$Default* | Grupa odbiorców Centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz, gdy Usługa Azure Data Explorer i konto magazynu są połączone, można utworzyć przykładowe dane i przekazać je do magazynu obiektów blob.

Ten skrypt tworzy nowy kontener na koncie magazynu, przekazuje istniejący plik (jako obiekt blob) do tego kontenera, a następnie wyświetla listę obiektów blob w kontenerze.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Usługa Azure Data Explorer nie będzie usuwać obiektów blob po spożyciu. Zachowaj obiekty blob przez trzy do pięciu dni przy użyciu [cyklu życia magazynu obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) do zarządzania usuwaniem obiektów blob.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
