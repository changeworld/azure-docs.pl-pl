---
title: Filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services — Azure | Dokumentacja firmy Microsoft
description: W tym artykule omówiono, filtrowanie, porządkowanie, stronicowanie jednostek usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496577"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Stronicowanie filtrowania, sortowania, jednostek usługi Media Services

Usługa Media Services obsługuje następujące opcje zapytania OData dla usługi Media Services v3 jednostek: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Opis operatora:

* EQ = równa
* Ne = nie jest równa
* GE = większa niż lub równe
* Le = mniejsze niż lub równe
* Gt = większa niż
* Lt = mniej niż

Właściwości jednostki, które są typu Data/godzina są zawsze w formacie UTC.

## <a name="page-results"></a>Wyniki strony

Jeśli odpowiedzi na zapytanie zawiera wiele elementów, usługa zwraca "\@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Nie można skonfigurować rozmiaru strony. Rozmiar strony jest zależna od typu jednostki, przeczytaj poszczególne sekcje, które należy wykonać, aby uzyskać szczegółowe informacje.

Jeśli jednostki są tworzone lub usuwane podczas stronicować kolekcji, zmiany są odzwierciedlane w zwróconych wyników (Jeśli te zmiany w części w kolekcji, która nie została pobrana). 

> [!TIP]
> Łącze do następnej zawsze należy używać wyliczania kolekcji i nie są zależne od wielkości określonej strony.

## <a name="assets"></a>Elementy zawartości

### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak filtrowanie i kolejność opcje można stosować do [zasobów](https://docs.microsoft.com/rest/api/media/assets) właściwości: 

|Name (Nazwa)|Filtr|Zamówienie|
|---|---|---|
|id|||
|name|eq, gt, lt| Rosnącej na malejącą lub odwrotnie|
|properties.alternateId |eq||
|properties.assetId |eq||
|Properties.container |||
|Properties.created| eq, gt, lt| Rosnącej na malejącą lub odwrotnie|
|Properties.Description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

W poniższym przykładzie C# filtr Data utworzenia:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Paginacja 

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. Obecnie rozmiar strony wynosi 1000.

#### <a name="c-example"></a>Przykład w języku C#

W poniższym przykładzie C# przedstawiono sposób wyliczyć wszystkie zasoby w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Przykład REST

Rozważmy następujący przykład, dla których jest używana funkcja $skiptoken. Upewnij się, Zastąp *amstestaccount* nazwą swojego konta i zestaw *parametru api-version* wartość do najnowszej wersji.

Jeśli żądanie Lista zasobów w następujący sposób:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Możesz zwróci odpowiedź podobny do następującego:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Następnej strony będzie następnie zażądać, wysyłając żądanie get:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Aby uzyskać więcej przykładów REST, zobacz [zasobów — lista](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="content-key-policies"></a>Zasady kluczy zawartości

### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do [zasad dotyczących zawartości klucza](https://docs.microsoft.com/rest/api/media/contentkeypolicies) właściwości: 

|Name (Nazwa)|Filtr|Zamówienie|
|---|---|---|
|id|||
|name|Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|Properties.created |Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|Properties.Description |Eq, ne, ge, le, gt, lt||
|properties.lastModified|Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|Properties.Options |||
|properties.policyId|Eq, ne||
|type|||

### <a name="pagination"></a>Paginacja

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. Obecnie rozmiar strony jest 10.

Następujące C# przykład pokazuje, jak wyliczyć przez wszystkie **zasad dotyczących zawartości klucza** w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

POZOSTAŁE przykłady można znaleźć [zasady kluczy zawartości — lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="jobs"></a>Stanowiska

### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do [zadań](https://docs.microsoft.com/rest/api/media/jobs) właściwości: 

| Name (Nazwa)    | Filtr                        | Zamówienie |
|---------|-------------------------------|-------|
| name                    | eq            | Rosnącej na malejącą lub odwrotnie|
| Properties.state        | Eq, ne        |                         |
| Properties.created      | gt, ge, lt, le| Rosnącej na malejącą lub odwrotnie|
| properties.lastModified | gt, ge, lt, le | Rosnącej na malejącą lub odwrotnie| 

### <a name="pagination"></a>Paginacja

Podział na strony zadania jest obsługiwane w Media Services v3.

Następujące C# przykład pokazuje, jak wyliczyć za pośrednictwem zadań w ramach konta.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

POZOSTAŁE przykłady można znaleźć [zadania — lista](https://docs.microsoft.com/rest/api/media/jobs/list)

## <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do właściwości StreamingLocator: 

|Name (Nazwa)|Filtr|Zamówienie|
|---|---|---|
|id |||
|name|Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|Properties.created |Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>Paginacja

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. Obecnie rozmiar strony jest 10.

W poniższym przykładzie C# pokazano, jak wyliczyć za pośrednictwem wszystkich StreamingLocators w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

POZOSTAŁE przykłady można znaleźć [Lokalizatory przesyłania strumieniowego — lista](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="streaming-policies"></a>Zasady przesyłania strumieniowego

### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do właściwości StreamingPolicy: 

|Name (Nazwa)|Filtr|Zamówienie|
|---|---|---|
|id|||
|name|Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.created |Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Paginacja

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. Obecnie rozmiar strony jest 10.

W poniższym przykładzie C# pokazano, jak wyliczyć za pośrednictwem wszystkich StreamingPolicies w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

POZOSTAŁE przykłady można znaleźć [przesyłania strumieniowego zasad — Lista](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="transform"></a>Przekształcanie

### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do [przekształca](https://docs.microsoft.com/rest/api/media/transforms) właściwości: 

| Name (Nazwa)    | Filtr                        | Zamówienie |
|---------|-------------------------------|-------|
| name                    | eq            | Rosnącej na malejącą lub odwrotnie|
| Properties.created      | gt, ge, lt, le| Rosnącej na malejącą lub odwrotnie|
| properties.lastModified | gt, ge, lt, le | Rosnącej na malejącą lub odwrotnie|

## <a name="next-steps"></a>Kolejne kroki

[Stream pliku](stream-files-dotnet-quickstart.md)
