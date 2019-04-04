---
title: Opracowywanie zawartości przy użyciu interfejsów API w wersji 3 — Azure | Dokumentacja firmy Microsoft
description: W tym artykule omówiono reguły mające zastosowanie do interfejsów API oraz jednostek, podczas tworzenia za pomocą usługi Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a5ab0b25a2a2db764854982b1a6801ce4f857dda
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891960"
---
# <a name="developing-with-media-services-v3-apis"></a>Tworzenie aplikacji za pomocą usługi Media Services v3 interfejsów API

W tym artykule omówiono reguły mające zastosowanie do interfejsów API oraz jednostek, podczas tworzenia za pomocą usługi Media Services v3.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Nazwy zasobów w usłudze Azure Media Services w wersji 3 (na przykład Zasoby, Zadania, Przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Zgodnie z zasadami usługi Azure Resource Manager nazwy zasobów są zawsze unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID). 

Nazwy zasobów usługi Media Services nie mogą zawierać znaków „<”, „>”, „%”, „&”, „:”, „&#92;”, „?”, „/”, „*”, „+”, „.”, pojedynczych cudzysłowów ani żadnych znaków sterujących. Wszystkie inne znaki są dozwolone. Maksymalna długość nazwy zasobu to 260 znaków. 

Aby uzyskać więcej informacji na temat nazewnictwa w usłudze Azure Resource Manager zobacz: [Wymagania dotyczące nazewnictwa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) i [Konwencje nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Zasady projektowania interfejsów API w wersji 3

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API w wersji 3 nie zwracają wpisów tajnych ani poświadczeń w operacji **Get** lub **List**. Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Należy wywołać oddzielną metodę akcji w celu pobrania wpisów tajnych lub poświadczeń. Oddzielne akcje umożliwiają ustawienie różnych uprawnień zabezpieczeń RBAC w przypadku, gdy niektóre interfejsy API pobierają/wyświetlają wpisy tajne, podczas gdy inne interfejsy API tego nie robią. Aby uzyskać informacje na temat zarządzania dostępem przy użyciu funkcji RBAC, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Przykłady to między innymi:

* Nie zwraca wartości ContentKey w Get StreamingLocator.
* Nie zwraca kluczy ograniczeń w Get ContentKeyPolicy.
* nie zwraca zapytanie ciągu w adresie URL (na przykład aby usunąć podpis) adresów URL danych wejściowych HTTP zadania.

Zobacz przykład [Get content key policy — .NET](get-content-key-policy-dotnet-howto.md) (Pobieranie zasad dotyczących klucza zawartości — .NET).

## <a name="long-running-operations"></a>Długotrwałych operacji

Operacje oznaczone `x-ms-long-running-operation` w usłudze Azure Media Services [swagger pliki](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) długie długotrwałych operacji. 

Aby uzyskać szczegółowe informacje na temat śledzenie operacji asynchronicznych na platformie Azure, zobacz [operacje asynchroniczne](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Usługa Media Services obsługuje następujące operacje długotrwałych:

* Utwórz element LiveEvent
* Element LiveEvent aktualizacji
* Usuń element LiveEvent
* Element LiveEvent Start
* Zatrzymaj element LiveEvent
* Resetuj element LiveEvent
* Utwórz LiveOutput
* Usuń LiveOutput
* Utwórz StreamingEndpoint
* Aktualizuj StreamingEndpoint
* Usuń StreamingEndpoint
* Rozpocznij StreamingEndpoint
* Zatrzymaj StreamingEndpoint
* StreamingEndpoint skalowania

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Stronicowanie filtrowania, sortowania, jednostek usługi Media Services

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

### <a name="page-results"></a>Wyniki strony

Jeśli odpowiedzi na zapytanie zawiera wiele elementów, usługa zwraca "\@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Nie można skonfigurować rozmiaru strony. Rozmiar strony jest zależna od typu jednostki, przeczytaj poszczególne sekcje, które należy wykonać, aby uzyskać szczegółowe informacje.

Jeśli jednostki są tworzone lub usuwane podczas stronicować kolekcji, zmiany są odzwierciedlane w zwróconych wyników (Jeśli te zmiany w części w kolekcji, która nie została pobrana). 

> [!TIP]
> Łącze do następnej zawsze należy używać wyliczania kolekcji i nie są zależne od wielkości określonej strony.

### <a name="assets"></a>Elementy zawartości

#### <a name="filteringordering"></a>Filtrowanie porządkowanie

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

#### <a name="pagination"></a>Paginacja 

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. Obecnie rozmiar strony wynosi 1000.

##### <a name="c-example"></a>Przykład w języku C#

W poniższym przykładzie C# przedstawiono sposób wyliczyć wszystkie zasoby w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>Przykład REST

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

### <a name="content-key-policies"></a>Zasady kluczy zawartości

#### <a name="filteringordering"></a>Filtrowanie porządkowanie

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

#### <a name="pagination"></a>Paginacja

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

### <a name="jobs"></a>Stanowiska

#### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do [zadań](https://docs.microsoft.com/rest/api/media/jobs) właściwości: 

| Name (Nazwa)    | Filtr                        | Zamówienie |
|---------|-------------------------------|-------|
| name                    | eq            | Rosnącej na malejącą lub odwrotnie|
| Properties.state        | Eq, ne        |                         |
| Properties.created      | gt, ge, lt, le| Rosnącej na malejącą lub odwrotnie|
| properties.lastModified | gt, ge, lt, le | Rosnącej na malejącą lub odwrotnie| 


#### <a name="pagination"></a>Paginacja

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

### <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

#### <a name="filteringordering"></a>Filtrowanie porządkowanie

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

#### <a name="pagination"></a>Paginacja

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

### <a name="streaming-policies"></a>Zasady przesyłania strumieniowego

#### <a name="filteringordering"></a>Filtrowanie porządkowanie

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

#### <a name="pagination"></a>Paginacja

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


### <a name="transform"></a>Przekształcanie

#### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do [przekształca](https://docs.microsoft.com/rest/api/media/transforms) właściwości: 

| Name (Nazwa)    | Filtr                        | Zamówienie |
|---------|-------------------------------|-------|
| name                    | eq            | Rosnącej na malejącą lub odwrotnie|
| Properties.created      | gt, ge, lt, le| Rosnącej na malejącą lub odwrotnie|
| properties.lastModified | gt, ge, lt, le | Rosnącej na malejącą lub odwrotnie|

## <a name="next-steps"></a>Kolejne kroki

[Stream pliku](stream-files-dotnet-quickstart.md)
