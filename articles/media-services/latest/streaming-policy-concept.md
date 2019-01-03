---
title: Strumieniowe przesyłanie zasad w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wyjaśnienie, co to są zasady przesyłania strumieniowego i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/22/2018
ms.author: juliako
ms.openlocfilehash: d74ce913a2189dd1062b30f9def919cbbabe7b64
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742528"
---
# <a name="streaming-policies"></a>Zasady przesyłania strumieniowego

W usłudze Azure Media Services v3 przesyłanie strumieniowe zasady umożliwiają zdefiniowanie protokołów przesyłania strumieniowego i opcje szyfrowania dla Twojego StreamingLocators. Możesz określić nazwę zasady przesyłania strumieniowego, utworzone lub użyj jednego z wstępnie zdefiniowane zasady przesyłania strumieniowego. Wstępnie zdefiniowane zasady przesyłania strumieniowego obecnie dostępne są: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" i "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> Korzystając z niestandardowego [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), należy zaprojektować ograniczony zestaw tych zasad dla swojego konta usługi multimediów i ponownie używać ich na potrzeby Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje szyfrowania i protokołów. Konto usługi multimediów ma limit przydziału dla liczby wpisów zasad przesyłania strumieniowego. Należy nie można utworzeniem nowej zasady przesyłania strumieniowego dla każdego lokalizatora przesyłania strumieniowego.

## <a name="streamingpolicy-definition"></a>Definicja StreamingPolicy

W poniższej tabeli przedstawiono właściwości StreamingPolicy oraz zapewnia ich definicje.

|Name (Nazwa)|Opis|
|---|---|
|id|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name|Nazwa zasobu.|
|properties.commonEncryptionCbcs|Konfiguracja CommonEncryptionCbcs|
|properties.commonEncryptionCenc|Konfiguracja CommonEncryptionCenc|
|Properties.created |Godzina utworzenia zasad przesyłania strumieniowego|
|properties.defaultContentKeyPolicyName |ContentKey domyślne używane przez bieżące zasady przesyłania strumieniowego|
|properties.envelopeEncryption  |Konfiguracja EnvelopeEncryption|
|properties.noEncryption|Konfiguracje bez szyfrowania|
|type|Typ zasobu.|

Pełna definicja można zobaczyć [przesyłania strumieniowego zasady](https://docs.microsoft.com/rest/api/media/streamingpolicies).

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Usługa Media Services obsługuje następujące opcje zapytania OData dla przesyłania strumieniowego zasad: 

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

> [!TIP]
> Łącze do następnej zawsze należy używać wyliczania kolekcji i nie są zależne od wielkości określonej strony.

Jeśli odpowiedzi na zapytanie zawiera wiele elementów, usługa zwraca "\@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Nie można skonfigurować rozmiaru strony. 

Jeśli StreamingPolicy są tworzone lub usuwane podczas stronicować kolekcji, zmiany zostaną odzwierciedlone w zwróconych wyników, (Jeśli te zmiany w części w kolekcji, która nie została pobrana.) 

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

## <a name="next-steps"></a>Kolejne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
