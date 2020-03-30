---
title: 'Tworzenie połączenia danych usługi Event Hub dla Eksploratora danych platformy Azure przy użyciu języka C #'
description: W tym artykule dowiesz się, jak utworzyć połączenie danych usługi Event Hub dla Usługi Azure Data Explorer przy użyciu języka C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667691"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Tworzenie połączenia danych usługi Event Hub dla Eksploratora danych platformy Azure przy użyciu języka C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-event-hub-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer oferuje pozyskiwania (ładowanie danych) z centrów zdarzeń, usługi IoT Hubs i obiektów blob zapisanych w kontenerach obiektów blob. W tym artykule utworzysz połączenie danych Usługi Event Hub dla Eksploratora danych platformy Azure przy użyciu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać **bezpłatną** [wersję programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Tworzenie [klastra i bazy danych](create-cluster-database-csharp.md)
* Tworzenie [mapowania tabel i kolumn](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ustawianie [zasad bazy danych i tabel](database-table-policies-csharp.md) (opcjonalnie)
* Utwórz [Centrum zdarzeń z danymi do pozyskiwania](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Dodawanie połączenia danych centrum zdarzeń

W poniższym przykładzie pokazano, jak programowo dodać połączenie danych usługi Event Hub. Zobacz [łączenie się z Centrum zdarzeń, aby](ingest-data-event-hub.md#connect-to-the-event-hub) dodać połączenie danych usługi Event Hub za pomocą witryny Azure portal.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Ustawienie** | **Sugerowana wartość** | **Opis pola**|
|---|---|---|
| identyfikator dzierżawy | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| clientSecret | *xxxxxxxxxxxxxx* | Klucz tajny klienta aplikacji, która może uzyskać dostęp do zasobów w dzierżawie.|
| resourceGroupName | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| clusterName | *mykustocluster* | Nazwa klastra.|
| Databasename | *mykustodatabase* | Nazwa docelowej bazy danych w klastrze.|
| dataConnectionName | *połączenie myeventhub* | Żądana nazwa połączenia danych.|
| tableName | *StormEvents (Burza)* | Nazwa tabeli docelowej w docelowej bazie danych.|
| nazwa mapowaniaRuleName | *StormEvents_CSV_Mapping* | Nazwa mapowania kolumn powiązana z tabelą docelową.|
| Dataformat | *Csv* | Format danych wiadomości.|
| identyfikator eventHubResourceId | *Identyfikator zasobu* | Identyfikator zasobu centrum zdarzeń, który przechowuje dane do pozyskiwania. |
| grupa konsumentów | *$Default* | Grupa odbiorców Centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
