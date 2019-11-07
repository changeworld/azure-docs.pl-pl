---
title: Kompleksowe pozyskiwanie obiektów BLOB na platformie Azure Eksplorator danych za pomocąC#
description: W tym artykule dowiesz się, jak pozyskiwanie obiektów BLOB na platformie Azure Eksplorator danych z kompleksowym przykładem, który C#używa programu.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: e22621083a44555cb3eda615c610f673cd841ec1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581847"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-c"></a>Kompleksowe pozyskiwanie obiektów BLOB na platformie Azure Eksplorator danych za pomocąC#

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Eksplorator danych to szybka i skalowalna usługa eksploracji danych dla danych dzienników i telemetrii. Ten artykuł zawiera kompleksowy przykład sposobu pozyskiwania danych z usługi Azure Blob Storage do usługi Azure Eksplorator danych. 

Dowiesz się, jak programowo utworzyć grupę zasobów, konto magazynu i kontener, centrum zdarzeń oraz klaster Eksplorator danych platformy Azure i bazę danych. Dowiesz się również, jak programowo skonfigurować usługę Azure Eksplorator danych w celu pozyskiwania danych z nowego konta magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="install-c-nuget"></a>Zainstaluj C# pakiet NuGet

* Zainstaluj [Microsoft. Azure. Management. Kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Zainstaluj [pakiet Microsoft. Azure. Management. ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
* Zainstaluj [Microsoft. Azure. Management. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/).
* Zainstaluj [Microsoft. Azure. Storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/).
* Zainstaluj [Microsoft. Rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) na potrzeby uwierzytelniania.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Przykładowy kod 

Poniższy przykład kodu udostępnia proces krok po kroku, który powoduje pozyskiwanie danych na platformie Azure Eksplorator danych. 

Najpierw należy utworzyć grupę zasobów. Możesz również tworzyć zasoby platformy Azure, takie jak konto magazynu i kontener, centrum zdarzeń oraz klaster Eksplorator danych platformy Azure i baza danych. Następnie utworzysz subskrypcję Azure Event Grid wraz z mapowaniem tabeli i kolumn w bazie danych Azure Eksplorator danych. Na koniec Utwórz połączenie danych w celu skonfigurowania usługi Azure Eksplorator danych w celu pozyskiwania danych z nowego konta magazynu. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//Path to the Azure Resource Manager template JSON from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, and database by using an Azure Resource Manager template.");
var parameters = $"{{\"eventHubNamespaceName\":{{\"value\":\"{eventHubNamespaceName}\"}},\"eventHubName\":{{\"value\":\"{eventHubName}\"}},\"storageAccountName\":{{\"value\":\"{storageAccountName}\"}},\"containerName\":{{\"value\":\"{storageContainerName}\"}},\"kustoClusterName\":{{\"value\":\"{kustoClusterName}\"}},\"kustoDatabaseName\":{{\"value\":\"{kustoDatabaseName}\"}}}}";
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **Ustawienie** | **Opis pola** |
|---|---|---|
| tenantId | Identyfikator dzierżawy. Jest on również znany jako identyfikator katalogu.|
| subscriptionId | Identyfikator subskrypcji używany do tworzenia zasobów.|
| clientId | Identyfikator klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie.|
| clientSecret | Wpis tajny klienta aplikacji, który może uzyskiwać dostęp do zasobów w dzierżawie. |

## <a name="test-the-code-example"></a>Testowanie przykładu kodu

1. Przekaż plik do konta magazynu.

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**Ustawienie** | **Opis pola**|
    |---|---|---|
    | storageConnectionString | Parametry połączenia z programowo utworzonym kontem magazynu.|

2. Uruchom kwerendę testową w usłudze Azure Eksplorator danych.

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć grupę zasobów i wyczyścić zasoby, użyj następującego polecenia:

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>Następne kroki

*  Aby dowiedzieć się więcej o innych sposobach tworzenia klastra i bazy danych, zobacz [Tworzenie klastra Eksplorator danych i bazy danych platformy Azure](create-cluster-database-csharp.md).
* Aby dowiedzieć się więcej o metodach pozyskiwania, zobacz [Azure Eksplorator danych Data](ingest-data-overview.md)pozyskiwania danych.
* Aby dowiedzieć się więcej o aplikacji sieci Web, zobacz [Szybki Start: wykonywanie zapytań dotyczących danych w interfejsie użytkownika sieci Web usługi Azure Eksplorator danych](web-query-data.md).
* [Zapisuj zapytania](write-queries.md) w języku zapytań Kusto.