---
title: Tworzenie połączenia danych centrum zdarzeń dla usługi Azure Eksplorator danych przy użyciuC#
description: W tym artykule dowiesz się, jak utworzyć połączenie danych centrum zdarzeń dla usługi Azure Eksplorator danych przy użyciu C#programu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 741232bb7ed6ecf3d20711c1f6248f8e6d6215e8
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031684"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Tworzenie połączenia danych centrum zdarzeń dla usługi Azure Eksplorator danych przy użyciuC#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Eksplorator danych oferuje pozyskiwanie (ładowanie danych) z Event Hubs, centrów IoT i obiektów blob, które są zapisywane do kontenerów obiektów BLOB. W tym artykule opisano tworzenie połączenia danych centrum zdarzeń dla usługi Azure Eksplorator danych przy użyciu programu C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać i korzystać **bezpłatnie** z programu [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Tworzenie [klastra i bazy danych](create-cluster-database-csharp.md)

* Tworzenie [mapowania tabeli i kolumny](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Ustawianie [zasad bazy danych i tabeli](database-table-policies-csharp.md) (opcjonalnie)

* Utwórz [centrum zdarzeń z danymi do](ingest-data-event-hub.md#create-an-event-hub)pozyskiwania. 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Dodawanie połączenia danych centrum zdarzeń

W poniższym przykładzie pokazano, jak programowo dodać połączenie danych centrum zdarzeń. Aby dodać połączenie danych centrum zdarzeń przy użyciu Azure Portal, zobacz [nawiązywanie połączenia z centrum zdarzeń](ingest-data-event-hub.md#connect-to-the-event-hub) .

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
| tenantId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator dzierżawy. Znany również jako identyfikator katalogu.|
| subscriptionId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | *XXXXXXXX-XXXXX-xxxx-xxxx-xxxxxxxxx* | Identyfikator klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| clientSecret | *xxxxxxxxxxxxxx* | Wpis tajny klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| resourceGroupName | *testrg* | Nazwa grupy zasobów zawierającej klaster.|
| clusterName | *mykustocluster* | Nazwa klastra.|
| Bazy | *mykustodatabase* | Nazwa docelowej bazy danych w klastrze.|
| ConnectionName | *myeventhubconnect* | Wymagana nazwa połączenia danych.|
| tableName | *StormEvents* | Nazwa tabeli docelowej w docelowej bazie danych.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nazwa mapowania kolumny powiązanej z tabelą docelową.|
| Format | *CSV* | Format danych wiadomości.|
| eventHubResourceId | *Identyfikator zasobu* | Identyfikator zasobu centrum zdarzeń, który przechowuje dane do pozyskiwania. |
| odbiorca | *$Default* | Grupa konsumentów centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
