---
title: Przesyłanie strumieniowe lokalizatorów w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis Lokalizatory przesyłania strumieniowego są i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 1757ca84e7390f1ecd2d6d1e90a085372d3e4c57
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381187"
---
# <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

Aby zapewnić klientom za pomocą adresu URL, który może służyć do odtwarzania zakodowanego filmu lub plików audio, musisz utworzyć [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) i tworzyć adresy URL przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Stream pliku](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Definicja StreamingLocator

W poniższej tabeli przedstawiono właściwości StreamingLocator oraz zapewnia ich definicje.

|Name (Nazwa)|Typ|Opis|
|---|---|---|
|id |ciąg|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name   |ciąg|Nazwa zasobu.|
|properties.alternativeMediaId  |ciąg|Identyfikator innych formatów ten Lokalizator przesyłania strumieniowego.|
|properties.assetName   |ciąg|Nazwa elementu zawartości|
|properties.contentKeys |[] StreamingLocatorContentKey|Kluczy zawartości używana przez ten Lokalizator przesyłania strumieniowego.|
|Properties.created |ciąg|Czas utworzenia lokalizatora przesyłania strumieniowego.|
|properties.defaultContentKeyPolicyName |ciąg|Nazwa domyślna ContentKeyPolicy używane przez ten Lokalizator przesyłania strumieniowego.|
|properties.endTime |ciąg|Godzina zakończenia lokalizatora przesyłania strumieniowego.|
|properties.startTime   |ciąg|Godzina rozpoczęcia lokalizatora przesyłania strumieniowego.|
|properties.streamingLocatorId  |ciąg|StreamingLocatorId lokalizatora przesyłania strumieniowego.|
|properties.streamingPolicyName |ciąg|Nazwa zasady przesyłania strumieniowego, używany przez ten Lokalizator przesyłania strumieniowego. Podaj nazwę zasad przesyłania strumieniowego, został utworzony lub użyj jednego z wstępnie zdefiniowane zasady przesyłania strumieniowego. Wstępnie zdefiniowane zasady przesyłania strumieniowego dostępne są: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" i "Predefined_ MultiDrmStreaming "|
|type   |ciąg|Typ zasobu.|

Pełna definicja można zobaczyć [Lokalizatory przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators).

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Usługa Media Services obsługuje następujące opcje zapytania OData dla lokalizatorów przesyłania strumieniowego: 

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

> [!TIP]
> Łącze do następnej zawsze należy używać wyliczania kolekcji i nie są zależne od wielkości określonej strony.

Jeśli odpowiedzi na zapytanie zawiera wiele elementów, usługa zwraca "\@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Nie można skonfigurować rozmiaru strony. 

Jeśli StreamingLocators są tworzone lub usuwane podczas stronicować kolekcji, zmiany zostaną odzwierciedlone w zwróconych wyników, (Jeśli te zmiany w części w kolekcji, która nie została pobrana.) 

W poniższym przykładzie C# pokazano, jak wyliczyć za pośrednictwem wszystkich StreamingLocators w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

POZOSTAŁE przykłady można znaleźć [Lokalizatory przesyłania strumieniowego — lista](https://docs.microsoft.com/rest/api/media/streaminglocators/streaminglocators_list)

## <a name="next-steps"></a>Kolejne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
