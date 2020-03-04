---
title: Tworzenie Event Grid połączenia danych dla usługi Azure Eksplorator danych za pomocąC#
description: W tym artykule dowiesz się, jak utworzyć Event Grid połączenie danych dla Eksplorator danych platformy Azure przy użyciu C#programu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255076"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Tworzenie Event Grid połączenia danych dla usługi Azure Eksplorator danych za pomocąC#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-event-grid-resource-manager.md)


Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Eksplorator danych oferuje pozyskiwanie (ładowanie danych) z Event Hubs, centrów IoT i obiektów blob, które są zapisywane do kontenerów obiektów BLOB. W tym artykule opisano tworzenie Event Grid połączenia danych dla usługi Azure Eksplorator danych przy użyciu programu C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Tworzenie [klastra i bazy danych](create-cluster-database-csharp.md)
* Tworzenie [mapowania tabeli i kolumny](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ustawianie [zasad bazy danych i tabeli](database-table-policies-csharp.md) (opcjonalnie)
* Utwórz [konto magazynu z subskrypcją Event Grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Dodawanie połączenia danych Event Grid

W poniższym przykładzie pokazano, jak w programie programowo dodać połączenie danych Event Grid. Zobacz [Tworzenie połączenia danych Event Grid w usłudze Azure Eksplorator danych](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) na potrzeby dodawania połączenia danych Event Grid przy użyciu Azure Portal.

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
| tenantId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| clientSecret | *xxxxxxxxxxxxxx* | Wpis tajny klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie. |
| resourceGroupName | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| clusterName | *mykustocluster* | Nazwa klastra.|
| databaseName | *mykustodatabase* | Nazwa docelowej bazy danych w klastrze.|
| ConnectionName | *myeventhubconnect* | Wymagana nazwa połączenia danych.|
| tableName | *StormEvents* | Nazwa tabeli docelowej w docelowej bazie danych.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nazwa mapowania kolumny powiązanej z tabelą docelową.|
| Format | *CSV* | Format danych wiadomości.|
| eventHubResourceId | *Identyfikator zasobu* | Identyfikator zasobu centrum zdarzeń, w którym Event Grid jest skonfigurowany do wysyłania zdarzeń. |
| storageAccountResourceId | *Identyfikator zasobu* | Identyfikator zasobu konta magazynu, który przechowuje dane do pozyskiwania. |
| odbiorca | *$Default* | Grupa konsumentów centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz, gdy usługa Azure Eksplorator danych i konto magazynu są połączone, można utworzyć przykładowe dane i przekazać je do magazynu obiektów BLOB.

Ten skrypt tworzy nowy kontener na koncie magazynu, przekazuje istniejący plik (jako obiekt BLOB) do tego kontenera, a następnie wyświetla listę obiektów BLOB w kontenerze.

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
> Usługa Azure Eksplorator danych nie usunie obiektów BLOB po pozyskaniu. Przechowuj obiekty blob przez trzy do pięciu dni przy użyciu [cyklu życia usługi Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) , aby zarządzać usuwaniem obiektów BLOB.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
