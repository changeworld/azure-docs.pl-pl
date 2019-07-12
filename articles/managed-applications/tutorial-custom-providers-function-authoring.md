---
title: Tworzenie elementów punkt końcowy usługi RESTful dla dostawcy niestandardowi
description: W tym samouczku zaczną przedstawić sposób tworzenie punkt końcowy usługi RESTful dla niestandardowych dostawców. Ta operacja przechodzi do szczegółów na temat sposobu obsługi żądań i odpowiedzi dla obsługiwanych metod RESTful HTTP.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800036"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Tworzenie elementów punkt końcowy usługi RESTful dla dostawcy niestandardowi

Dostawcy niestandardowi umożliwiają dostosowywanie przepływów pracy na platformie Azure. Niestandardowy dostawca jest kontrakt między platformą Azure i `endpoint`. W tym samouczku zostanie umieszczona w procesie tworzenia niestandardowego dostawcy zgodne ze specyfikacją REST `endpoint`. Jeśli nie jesteś zaznajomiony z dostawców niestandardowych usługi Azure, zobacz [omówienie dotyczące zasobów niestandardowych dostawców](./custom-providers-overview.md).

W tym samouczku jest dzielony na następujące czynności:

- Praca z akcji niestandardowych i zasobów niestandardowych
- Jak podzielić zasoby niestandardowe w magazynie
- Obsługuje niestandardowego dostawcy typu RESTful metod
- Integracja RESTful operacji

W tym samouczku zostanie skompilowany w ramach następujących samouczków:

- [Konfigurowanie usługi Azure Functions dla dostawców niestandardowych usługi Azure](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Ten samouczek opiera się poza poprzednim samouczku. Niektóre kroki samouczka działa tylko, jeśli funkcji platformy Azure została skonfigurowana do pracy z niestandardowych dostawców.

## <a name="working-with-custom-actions-and-custom-resources"></a>Praca z akcji niestandardowych i zasobów niestandardowych

W tym samouczku będą aktualizowane funkcja działa jako punkt końcowy usługi RESTful, w przypadku naszej niestandardowego dostawcy. Na platformie Azure zasobów i akcji są wzorowane na podstawowych specyfikacji RESTful: Umieść — tworzy nowy zasób, GET (wystąpienia) — umożliwia pobranie istniejącego zasobu, Usuń — powoduje usunięcie istniejącego zasobu, WPIS — wyzwalać akcję i GET (kolekcję) — zawiera listę wszystkich istniejących zasobów. W tym samouczku użyjemy tabele platformy Azure jako naszego magazynu, ale wszystkie bazy danych lub magazynu usługa może działać.

## <a name="how-to-partition-custom-resources-in-storage"></a>Jak podzielić zasoby niestandardowe w magazynie

Ponieważ tworzymy usługi RESTful, musimy do przechowywania zasobów utworzonych w magazynie. Usługi Azure Table Storage należy wygenerować kluczy partycji i wiersza danych. W przypadku niestandardowych dostawców danych powinny być dzielone niestandardowego dostawcy. Gdy żądanie przychodzące są wysyłane do dostawcy niestandardowego, spowoduje to dodanie niestandardowego dostawcy `x-ms-customproviders-requestpath` nagłówka wychodzące żądanie `endpoint`.

Przykładowe `x-ms-customproviders-requestpath` nagłówek dla zasobów niestandardowych:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Oparte na powyższym przykładzie `x-ms-customproviders-requestpath` nagłówka, można utworzyć właściwości partitionKey i rowKey naszego magazynu zgodnie z poniższymi:

Parametr | Szablon | Opis
---|---
właściwości partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | PartitionKey to, jak dane są podzielone na partycje. W większości przypadków dane powinny być dzielone według wystąpienia niestandardowego dostawcy.
RowKey | '{myResourceType}:{myResourceName}' | RowKey jest identyfikatorem poszczególnych danych. W większości przypadków jest to nazwa zasobu.

Ponadto należy również utworzyć nową klasę do modelowania naszych zasobów niestandardowych. W tym samouczku dodamy `CustomResource` klasy do naszych funkcji, która jest klasą rodzajową, który akceptuje dowolne klatkach, w których dane:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Spowoduje to utworzenie podstawowe klasy na podstawie `TableEntity`, który jest używany do przechowywania danych. `CustomResource` Klasa dziedziczy dwie właściwości z `TableEntity`: właściwości partitionKey i rowKey.

## <a name="support-custom-provider-restful-methods"></a>Obsługuje niestandardowego dostawcy typu RESTful metod

> [!NOTE]
> Jeśli nie kopiujesz kod bezpośrednio z tego samouczka, zawartość odpowiedzi powinna być prawidłowym kodem JSON i ustawia `Content-Type` nagłówek jako `application/json`.

Teraz, gdy Instalator partycjonowanie danych, możemy szkielet podstawowe metody CRUD i wyzwalacza dla niestandardowych zasobów i akcji niestandardowych. Ponieważ dostawców niestandardowych działał jako serwer proxy, żądań i odpowiedzi musi być modelowane i obsługiwane przez zgodne ze specyfikacją REST `endpoint`. Wykonaj poniższe fragmenty kodu do obsługi podstawowych operacji RESTful:

### <a name="trigger-custom-action"></a>Akcja niestandardowa wyzwalacza

W przypadku niestandardowych dostawców wyzwalane za pomocą akcji niestandardowej `POST` żądań. Akcja niestandardowa opcjonalnie może akceptować treści żądania, który zawiera zestaw parametrów wejściowych. Akcja powinna return powrotem odpowiedzi signally wynik monitorowanej akcji, a także czy zakończyła się powodzeniem lub nie powiodło się. W tym samouczku dodamy metody `TriggerCustomAction` naszych funkcji:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
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

`TriggerCustomAction` Metoda przyjmuje żądanie przychodzące i przekazuje po prostu wykonać ich kopię odpowiedzi z kodem stanu Powodzenie. 

### <a name="create-custom-resource"></a>Tworzenie zasobów niestandardowych

W przypadku niestandardowych dostawców zasobów niestandardowych jest tworzony za pomocą `PUT` żądań. Dostawca niestandardowy będzie akceptować treści żądania JSON, który zawiera zbiór właściwości zasobów niestandardowych. Na platformie Azure zasoby postępuj zgodnie z modelem RESTful. Ten sam adres URL żądania, który został użyty do utworzenia zasobu powinna także do pobierania i usuwania zasobu. W tym samouczku dodamy metody `CreateCustomResource` do tworzenia nowych zasobów:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
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

`CreateCustomResource` Metoda aktualizuje żądania przychodzącego, aby uwzględnić Azure określonych pól: `id`, `name`, i `type`. Są to właściwości najwyższego poziomu, które są używane przez usługi platformy Azure. Umożliwiają niestandardowego dostawcy w celu integracji z innymi usługami, takie jak usługi Azure Policy, szablonów usługi Azure Resource Manager i dzienników aktywności platformy Azure.

Właściwość | Sample | Opis
---|---|---
name | '{myCustomResourceName}' | Nazwa zasobu niestandardowego.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Przestrzeń nazw typu zasobu.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | Identyfikator zasobu.

Oprócz dodawania właściwości, możemy również zapisać dokument do usługi Azure Table Storage. 

### <a name="retrieve-custom-resource"></a>Pobieranie zasobów niestandardowych

W przypadku niestandardowych dostawców zasobów niestandardowych są pobierane za pośrednictwem `GET` żądań. Dostawca niestandardowy będzie *nie* zaakceptować treści żądania JSON. W przypadku właściwości `GET` żądania, **punktu końcowego** należy używać `x-ms-customproviders-requestpath` nagłówka do zwrócenia już utworzonego zasobu. W tym samouczku dodamy metody `RetrieveCustomResource` można pobrać istniejących zasobów:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
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

Na platformie Azure zasoby należy stosować RESTful model. Adres URL żądania, utworzony zasób również powinna zwrócić zasobu, jeśli `GET` wykonać żądania.

### <a name="remove-custom-resource"></a>Usuń zasób niestandardowy

Dla dostawców niestandardowych, zasób niestandardowy zostanie usunięta za pośrednictwem `DELETE` żądań. Dostawca niestandardowy będzie *nie* zaakceptować treści żądania JSON. W przypadku właściwości `DELETE` żądania, **punktu końcowego** należy używać `x-ms-customproviders-requestpath` nagłówek, aby usunąć już utworzonego zasobu. W tym samouczku dodamy metody `RemoveCustomResource` Aby usunąć istniejące zasoby:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
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

Na platformie Azure zasoby należy stosować RESTful model. Adres URL żądania, utworzonego zasobu należy również usunąć zasób, jeśli `DELETE` wykonać żądania.

### <a name="list-all-custom-resources"></a>Wyświetla listę wszystkich zasobów niestandardowych

W przypadku niestandardowych dostawców listę istniejących zasobów niestandardowych mogą być wyliczane w kolekcji `GET` żądań. Dostawca niestandardowy będzie *nie* zaakceptować treści żądania JSON. W przypadku kolekcji `GET` żądania, `endpoint` należy używać `x-ms-customproviders-requestpath` nagłówka do wyliczenia zasobów, które zostały już utworzone. W tym samouczku dodamy metody `EnumerateAllCustomResources` wyliczyć istniejących zasobów.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
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
> Klucz wiersza więcej niż i mniej niż składni tabeli platformy Azure do wykonania zapytania "startswith" dla ciągów. 

Do wyświetlania listy wszystkich istniejących zasobów, możemy wygenerować zapytania tabeli platformy Azure, które zapewnia, że zasobów istnieje w naszym partycji niestandardowego dostawcy. Zapytanie, a następnie sprawdza, czy klucz wiersza, który rozpoczyna się o takiej samej `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integracja RESTful operacji

Po dodaniu wszystkich metod RESTful do funkcji, firma Microsoft aktualizuje głównym `Run` metodę do wywołania funkcji, które mają zajmiemy się resztą innego żądania:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
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
        // Action request for an custom action.
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

Zaktualizowany interfejs `Run` metoda będzie teraz obejmować `tableStorage` powiązania, który został dodany do usługi Azure Table storage danych wejściowych. Pierwsza część metody będą teraz odczytywać `x-ms-customproviders-requestpath` nagłówek i użyj `Microsoft.Azure.Management.ResourceManager.Fluent` biblioteki można przeanalizować wartości jako identyfikatora zasobu. `x-ms-customproviders-requestpath` Nagłówek został wysłany przez niestandardowego dostawcy i wyznacza ścieżkę żądania przychodzącego. Przy użyciu Identyfikatora zasobu przeanalizowany, można teraz generujemy właściwości partitionKey i rowKey danych do wyszukiwania ani do przechowywania zasobów niestandardowych.

Oprócz dodawania metod i klas, musimy zaktualizować za pomocą metody dla tej funkcji. Dodaj następujący element do górnej części pliku:

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

Jeśli masz utracone w dowolnym momencie po ukończeniu tego samouczka, przykładowy kompletny kod znajduje się w temacie [niestandardowego dostawcy C# odwołania punktem końcowym RESTful](./reference-custom-providers-csharp-endpoint.md). Po zakończeniu funkcji Zapisz adres URL funkcji, który może służyć do wyzwolenia funkcji, ponieważ zostaną użyte w kolejnych samouczkach.

## <a name="next-steps"></a>Następne kroki

W tym artykule, firma Microsoft utworzony punkt końcowy usługi RESTful do pracy z platformy Azure niestandardowego dostawcę `endpoint`. Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć niestandardowego dostawcę.

- [Samouczek: Tworzenie niestandardowego dostawcy](./tutorial-custom-providers-create.md)
