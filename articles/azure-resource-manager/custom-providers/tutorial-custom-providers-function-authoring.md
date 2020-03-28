---
title: Tworzenie punktu końcowego usługi RESTful
description: W tym samouczku pokazano, jak autor punktu końcowego RESTful dla dostawców niestandardowych. Szczegółowe informacje na temat obsługi żądań i odpowiedzi dla obsługiwanych metod HTTP RESTful.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650023"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Tworzenie punktu końcowego RESTful dla dostawców niestandardowych

Dostawca niestandardowy to umowa między platformą Azure a punktem końcowym. Za pomocą dostawców niestandardowych można dostosowywać przepływy pracy na platformie Azure. W tym samouczku pokazano, jak autoryzować niestandardowy punkt końcowy RESTful dostawcy. Jeśli nie znasz dostawców niestandardowych platformy Azure, zobacz [omówienie dostawców zasobów niestandardowych.](overview.md)

> [!NOTE]
> Ten samouczek opiera się na samouczku [Konfigurowanie funkcji platformy Azure dla dostawców niestandardowych platformy Azure](./tutorial-custom-providers-function-setup.md). Niektóre kroki opisane w tym samouczku działają tylko wtedy, gdy aplikacja funkcji platformy Azure została skonfigurowana do pracy z dostawcami niestandardowymi.

## <a name="work-with-custom-actions-and-custom-resources"></a>Praca z akcjami niestandardowymi i zasobami niestandardowymi

W tym samouczku zaktualizujesz aplikację funkcji, aby działała jako punkt końcowy RESTful dla dostawcy niestandardowego. Zasoby i akcje na platformie Azure są modelowane zgodnie z następującą podstawową specyfikacją RESTful:

- **PUT**: Tworzenie nowego zasobu
- **GET (wystąpienie)**: Pobieranie istniejącego zasobu
- **USUŃ**: Usuwanie istniejącego zasobu
- **POST**: Wyzwalanie akcji
- **GET (kolekcja)**: Lista wszystkich istniejących zasobów

 W tym samouczku używasz usługi Azure Table Storage. Ale każda baza danych lub usługa magazynu może działać.

## <a name="partition-custom-resources-in-storage"></a>Partycja zasobów niestandardowych w magazynie

Ponieważ tworzysz usługę RESTful, musisz przechowywać utworzone zasoby. W przypadku magazynu tabel platformy Azure należy wygenerować klucze partycji i wierszy dla danych. W przypadku dostawców niestandardowych dane powinny być podzielone na partycje do dostawcy niestandardowego. Gdy żądanie przychodzące jest wysyłane do dostawcy niestandardowego, dostawca niestandardowy dodaje `x-ms-customproviders-requestpath` nagłówek do wychodzących żądań do punktu końcowego.

W poniższym `x-ms-customproviders-requestpath` przykładzie pokazano nagłówek zasobu niestandardowego:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Na podstawie nagłówka `x-ms-customproviders-requestpath` przykładu można utworzyć parametry *partitionKey* i *rowKey* dla magazynu, jak pokazano w poniższej tabeli:

Parametr | Szablon | Opis
---|---|---
*partitionKey (klucz)* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Parametr *partitionKey* określa sposób partycjonowania danych. Zazwyczaj dane są podzielone na partycje przez wystąpienie dostawcy niestandardowego.
*klawisz rowKey* | `{myResourceType}:{myResourceName}` | Parametr *rowKey* określa indywidualny identyfikator danych. Zazwyczaj identyfikator jest nazwą zasobu.

Należy również utworzyć nową klasę do modelowania zasobu niestandardowego. W tym samouczku należy dodać następującą klasę **CustomResource** do aplikacji funkcji:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** to prosta, ogólna klasa, która akceptuje wszelkie dane wejściowe. Jest on oparty na **TableEntity**, który jest używany do przechowywania danych. **Klasa CustomResource** dziedziczy dwie właściwości z **TableEntity:** **partitionKey** i **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Obsługa metod restful dostawcy niestandardowego

> [!NOTE]
> Jeśli nie kopiujesz kodu bezpośrednio z tego samouczka, zawartość odpowiedzi musi `Content-Type` być `application/json`prawidłowa JSON, która ustawia nagłówek na .

Teraz, gdy masz skonfigurowane partycjonowanie danych, utwórz podstawowe metody CRUD i wyzwalacza dla zasobów niestandardowych i akcji niestandardowych. Ponieważ dostawcy niestandardowi działają jako serwery proxy, punkt końcowy RESTful musi modelować i obsługiwać żądanie i odpowiedź. Poniższe fragmenty kodu pokazują, jak obsługiwać podstawowe operacje RESTful.

### <a name="trigger-a-custom-action"></a>Wyzwalanie akcji niestandardowej

W przypadku dostawców niestandardowych akcja niestandardowa jest wyzwalana za pośrednictwem żądań POST. Akcja niestandardowa może opcjonalnie zaakceptować treść żądania, która zawiera zestaw parametrów wejściowych. Akcja następnie zwraca odpowiedź, która sygnalizuje wynik akcji i czy powiodło się lub nie powiodło się.

Dodaj następującą metodę **TriggerCustomAction** do aplikacji funkcji:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

**TriggerCustomAction** Metoda akceptuje przychodzące żądanie i po prostu echa odpowiedzi z kodem stanu.

### <a name="create-a-custom-resource"></a>Tworzenie zasobu niestandardowego

W przypadku dostawców niestandardowych zasób niestandardowy jest tworzony za pomocą żądań PUT. Dostawca niestandardowy akceptuje treść żądania JSON, która zawiera zestaw właściwości zasobu niestandardowego. Zasoby na platformie Azure są zgodne z modelem RESTful. Ten sam adres URL żądania służy do tworzenia, pobierania lub usuwania zasobu.

Dodaj następującą metodę **CreateCustomResource,** aby utworzyć nowe zasoby:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Metoda **CreateCustomResource** aktualizuje żądanie przychodzące, aby uwzględnić **identyfikator,** **nazwę**i **typ**pól specyficznych dla platformy Azure. Te pola są właściwości najwyższego poziomu używane przez usługi na platformie Azure. Umożliwiają one dostawcy niestandardowemu współdziałanie z innymi usługami, takimi jak zasady platformy Azure, szablony usługi Azure Resource Manager i dziennik aktywności platformy Azure.

Właściwość | Przykład | Opis
---|---|---
**Nazwa** | {myCustomResourceName} | Nazwa zasobu niestandardowego
**Typu** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | Obszar nazw typu zasobu
**Identyfikator** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupGroupName}/<br>dostawcy/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Identyfikator zasobu

Oprócz dodawania właściwości, dokument JSON został również zapisany do magazynu tabel platformy Azure.

### <a name="retrieve-a-custom-resource"></a>Pobieranie zasobu niestandardowego

W przypadku dostawców niestandardowych zasób niestandardowy jest pobierany za pośrednictwem żądań GET. Dostawca *niestandardowy nie* akceptuje treści żądania JSON. W przypadku żądań GET punkt `x-ms-customproviders-requestpath` końcowy używa nagłówka do zwrócenia już utworzonego zasobu.

Dodaj następującą metodę **RetrieveCustomResource,** aby pobrać istniejące zasoby:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Na platformie Azure zasoby są zgodne z modelem RESTful. Adres URL żądania, który tworzy zasób zwraca również zasób, jeśli zostanie wykonane żądanie GET.

### <a name="remove-a-custom-resource"></a>Usuwanie zasobu niestandardowego

W przypadku dostawców niestandardowych zasób niestandardowy jest usuwany za pośrednictwem żądań DELETE. Dostawca *niestandardowy nie* akceptuje treści żądania JSON. W przypadku żądania DELETE punkt końcowy `x-ms-customproviders-requestpath` używa nagłówka do usuwania już utworzonego zasobu.

Dodaj następującą metodę **RemoveCustomResource,** aby usunąć istniejące zasoby:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Na platformie Azure zasoby są zgodne z modelem RESTful. Adres URL żądania, który tworzy zasób, również usuwa zasób, jeśli zostanie wykonane żądanie DELETE.

### <a name="list-all-custom-resources"></a>Wyświetlanie listy wszystkich zasobów niestandardowych

W przypadku dostawców niestandardowych można wyliczyć listę istniejących zasobów niestandardowych przy użyciu żądań GET z kolekcji. Dostawca *niestandardowy nie* akceptuje treści żądania JSON. W przypadku kolekcji żądań GET punkt `x-ms-customproviders-requestpath` końcowy używa nagłówka do wyliczenia już utworzonych zasobów.

Dodaj następującą metodę **EnumerateAllCustomResources,** aby wyliczyć istniejące zasoby:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> Składnia magazynu tabely usługi Azure Do wykonywania kwerendy "startswith" dla ciągów jest składnia rowkey querycomparisons.GreaterThan i QueryComparisons.LessThan jest składnia magazynu tabel platformy Azure do wykonywania kwerendy "startswith" dla ciągów.

Aby wyświetlić listę wszystkich istniejących zasobów, wygeneruj kwerendę magazynu tabel platformy Azure, która zapewnia, że zasoby istnieją w ramach partycji dostawcy niestandardowego. Następnie kwerenda sprawdza, czy klucz wiersza rozpoczyna się od tej samej `{myResourceType}` wartości.

## <a name="integrate-restful-operations"></a>Integracja operacji RESTful

Po dodaniu wszystkich metod RESTful do aplikacji funkcji, zaktualizuj główną metodę **Uruchom,** która wywołuje funkcje do obsługi różnych żądań REST:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

Zaktualizowana metoda **Uruchamiani** zawiera teraz powiązanie wejściowe *tableStorage,* które zostało dodane dla magazynu tabel platformy Azure. Pierwsza część metody odczytuje `x-ms-customproviders-requestpath` nagłówek i `Microsoft.Azure.Management.ResourceManager.Fluent` używa biblioteki do analizowania wartości jako identyfikatora zasobu. Nagłówek `x-ms-customproviders-requestpath` jest wysyłany przez dostawcę niestandardowego i określa ścieżkę żądania przychodzącego.

Za pomocą analizowanego identyfikatora zasobu można wygenerować wartości **partitionKey** i **rowKey** dla danych do wyszukiwania lub przechowywania zasobów niestandardowych.

Po dodaniu metod i klas, należy zaktualizować **przy użyciu** metod dla aplikacji funkcji. Dodaj następujący kod do górnej części pliku języka C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Jeśli zgubisz się w dowolnym momencie tego samouczka, możesz znaleźć pełny przykład kodu w [niestandardowym dostawcy C# RESTful endpoint reference](./reference-custom-providers-csharp-endpoint.md). Po zakończeniu aplikacji funkcji zapisz adres URL aplikacji funkcji. Może służyć do wyzwalania aplikacji funkcji w późniejszych samouczkach.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyno punkt końcowy RESTful do pracy z punktem końcowym dostawcy niestandardowego platformy Azure. Aby dowiedzieć się, jak utworzyć dostawcę niestandardowego, przejdź do artykułu [Samouczek: Tworzenie dostawcy niestandardowego](./tutorial-custom-providers-create.md).
