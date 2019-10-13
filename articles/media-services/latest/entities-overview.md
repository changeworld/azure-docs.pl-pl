---
title: Filtrowanie, porządkowanie, stronicowanie jednostek Media Services — platforma Azure | Microsoft Docs
description: W tym artykule omówiono filtrowanie, porządkowanie, stronicowanie jednostek Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed509ac8fea43a9c011bbbf76c1dc433cd78d43c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298954"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrowanie, porządkowanie, stronicowanie jednostek Media Services

W tym temacie omówiono opcje zapytania OData i obsługa podziału na strony dostępne podczas tworzenia listy jednostek Azure Media Services v3.

## <a name="considerations"></a>Zagadnienia do rozważenia

* Właściwości jednostek, które są typu DateTime, są zawsze w formacie UTC.
* Biały znak w ciągu zapytania powinien być kodowany przy użyciu adresu URL przed wysłaniem żądania.

## <a name="comparison-operators"></a>Operatory porównania

Aby porównać pole z wartością stałą, można użyć następujących operatorów:

Operatory równości:

- `eq`: testowanie, czy pole jest **równe** wartości stałej
- `ne`: testowanie, czy pole **nie jest równe** wartości stałej

Operatory zakresu:

- `gt`: testowanie, czy pole jest **większe niż** wartość stała
- `lt`: testowanie, czy pole jest **mniejsze niż** wartość stała
- `ge`: testowanie, czy pole jest **większe lub równe** wartości stałej
- `le`: testowanie, czy pole jest **mniejsze niż lub równe** wartości stałej

## <a name="filter"></a>Filtr

**$Filter** — Użyj filtru, aby podać parametr filtru OData w celu znalezienia tylko interesujących obiektów.

Poniżej przedstawiono przykładowe filtry w alternateId elementu zawartości:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Poniższe C# przykładowe filtry według daty utworzenia elementu zawartości:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>Porządkuj według

**$OrderBy** — Użyj jej do sortowania zwracanych obiektów przez określony parametr. Na przykład:    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Aby posortować wyniki w kolejności rosnącej lub malejącej, Dołącz `asc` lub `desc` do nazwy pola rozdzielone spacją. Na przykład `$orderby properties/created desc`.

## <a name="skip-token"></a>Pomiń token

**$skiptoken** — Jeśli odpowiedź na zapytanie zawiera wiele elementów, usługa zwraca wartość tokenu pomijania (`@odata.nextLink`), która jest używana do uzyskiwania następnej strony wyników. Ta wartość może być używana na stronie przez cały zestaw wyników.

W Media Services V3 nie można skonfigurować rozmiaru strony. Rozmiar strony jest różny w zależności od typu jednostki. Przeczytaj poszczególne sekcje, które są zgodne, aby uzyskać szczegółowe informacje.

Jeśli jednostki są tworzone lub usuwane podczas stronicowania kolekcji, zmiany są odzwierciedlane w zwracanych wynikach (jeśli te zmiany znajdują się w części kolekcji, która nie została pobrana). 

> [!TIP]
> Należy zawsze używać `nextLink` do wyliczenia kolekcji i nie zależy od określonego rozmiaru strony.
>
> @No__t-0 będzie obecna tylko wtedy, gdy istnieje więcej niż jedna strona jednostek.

Rozważmy następujący przykład użycia $skiptoken. Upewnij się, że zastąpisz *amstestaccount* z nazwą konta i ustawisz wartość *interfejsu API-Version* w najnowszej wersji.

W przypadku żądania listy zasobów takich jak:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Odpowiedź będzie wyglądać podobnie do tego:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Następnie Zażądaj następnej strony, wysyłając żądanie Get dla:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Poniższy C# przykład pokazuje, jak wyliczyć wszystkie lokalizatory przesyłania strumieniowego na koncie.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Używanie operatorów logicznych do łączenia opcji zapytania

Media Services v3 obsługuje operatory logiczne "or" i "i". 

Poniższy przykład REST sprawdza stan zadania:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Te same zapytania są konstruowane C# w następujący sposób: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opcje filtrowania i porządkowania jednostek

W poniższej tabeli przedstawiono sposób stosowania opcji filtrowania i porządkowania do różnych jednostek:

|Nazwa jednostki|Nazwa właściwości|Filtr|Zamów|
|---|---|---|---|
|[Elementy zawartości](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` i `desc`|
||Właściwości. alternateId |`eq`||
||Właściwości. assetId |`eq`||
||Właściwości. utworzone| `eq`, `gt`, `lt`| `asc` i `desc`|
|[Zasady kluczy zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. utworzone    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Properties. Description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Właściwości. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. policyId|`eq`, `ne`||
|[Zadania](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` i `desc`|
||Properties. State        | `eq`, `ne`        |                         |
||Właściwości. utworzone      | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|
||Właściwości. lastModified | `gt`, `ge`, `lt`, `le` | `asc` i `desc`| 
|[Lokalizatory przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. utworzone    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
|[Zasady przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. utworzone    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
|[Przekształca](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` i `desc`|
|| Właściwości. utworzone      | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|
|| Właściwości. lastModified | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|

## <a name="next-steps"></a>Następne kroki

* [Wyświetl listę zasobów](https://docs.microsoft.com/rest/api/media/assets/list)
* [Wyświetl listę zasad dotyczących kluczy zawartości](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Wyświetl listę zadań](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Wyświetlanie listy zasad przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Wyświetlanie listy lokalizatorów przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
