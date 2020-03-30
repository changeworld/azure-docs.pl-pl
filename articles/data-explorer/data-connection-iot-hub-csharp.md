---
title: 'Tworzenie połączenia danych usługi IoT Hub dla Eksploratora danych platformy Azure przy użyciu języka C #'
description: W tym artykule dowiesz się, jak utworzyć połączenie danych usługi IoT Hub dla Usługi Azure Data Explorer przy użyciu języka C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667383"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Tworzenie połączenia danych usługi IoT Hub dla Eksploratora danych platformy Azure przy użyciu języka C# (Wersja zapoznawcza)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer oferuje pozyskiwania (ładowanie danych) z centrów zdarzeń, usługi IoT Hubs i obiektów blob zapisanych w kontenerach obiektów blob. W tym artykule utworzysz połączenie danych usługi IoT Hub dla Eksploratora danych platformy Azure przy użyciu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz zainstalowanego programu Visual Studio 2019, możesz pobrać **bezpłatną** [wersję programu Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niej. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Tworzenie [klastra i bazy danych](create-cluster-database-csharp.md)
* Tworzenie [mapowania tabel i kolumn](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Ustawianie [zasad bazy danych i tabel](database-table-policies-csharp.md) (opcjonalnie)
* Utwórz [Centrum IoT z skonfigurowaną zasadą dostępu współdzielonego](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Dodawanie połączenia danych usługi IoT Hub 

W poniższym przykładzie pokazano, jak programowo dodać połączenie danych usługi IoT Hub. Zobacz [łączenie tabeli Usługi Azure Data Explorer z usługą IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) w celu dodania połączenia danych usługi Iot Hub przy użyciu witryny Azure portal.

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
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
| identyfikator iotHubResourceId | *Identyfikator zasobu* | Identyfikator zasobu centrum IoT hub, który przechowuje dane do pozyskiwania. |
| sharedAccessPolcyName | *iothubforread* | Nazwa zasady dostępu współdzielonego, która definiuje uprawnienia dla urządzeń i usług do łączenia się z Centrum IoT Hub. |
| grupa konsumentów | *$Default* | Grupa odbiorców centrum zdarzeń.|
| location | *Środkowe stany USA* | Lokalizacja zasobu połączenia danych.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
