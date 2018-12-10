---
title: Zawartość zasady kluczy w usłudze Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wyjaśnienie, co to są zasady klucz zawartości i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 541713677184d93eb78856e3c3373ab432d5f0cf
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141533"
---
# <a name="content-key-policies"></a>Zasady kluczy zawartości

Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu wysłania komputera za pośrednictwem przechowywania, przetwarzania i dostarczania. Usługa Media Services umożliwia dostarczanie zawartości na żywo i na żądanie dynamicznie zaszyfrowany za pomocą Advanced Encryption Standard (AES-128), lub jeden z systemów zarządzania (prawami cyfrowymi DRM) trzech głównych prawami cyfrowymi: PlayReady firmy Microsoft, Google Widevine i FairPlay firmy Apple. Media Services udostępnia również usługę dostarczania kluczy AES i technologii DRM (PlayReady, Widevine i FairPlay) licencji do autoryzowanych klientów.

W usłudze Azure Media Services v3 zasady kluczy zawartości umożliwiają określenie, jak klucz zawartości jest dostarczany do klientów za pośrednictwem składnika klucz usługi dostarczanie końcowych. Aby uzyskać więcej informacji, zobacz [zawartości Omówienie ochrony](content-protection-overview.md).

## <a name="contentkeypolicies-definition"></a>Definicja ContentKeyPolicies

W poniższej tabeli przedstawiono właściwości ContentKeyPolicy oraz zapewnia ich definicje.

|Name (Nazwa)|Opis|
|---|---|
|id|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name|Nazwa zasobu.|
|Properties.created |Data utworzenia zasad|
|Properties.Description |Opis zasad.|
|properties.lastModified|Data ostatniej modyfikacji zasad|
|Properties.Options |Opcje zasad klucza.|
|properties.policyId    |Starsze identyfikatora zasad.|
|type   |Typ zasobu.|

Pełna definicja można zobaczyć [zasad dotyczących zawartości klucza](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Usługa Media Services obsługuje następujące opcje zapytania OData ContentKeyPolicies: 

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
|Properties.created |Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|Properties.Description |Eq, ne, ge, le, gt, lt||
|properties.lastModified    |Eq, ne, ge, le, gt, lt|Rosnącej na malejącą lub odwrotnie|
|Properties.Options |||
|properties.policyId    |Eq, ne||
|type   |||

### <a name="pagination"></a>Paginacja

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. Obecnie rozmiar strony jest 10.

> [!TIP]
> Łącze do następnej zawsze należy używać wyliczania kolekcji i nie są zależne od wielkości określonej strony.

Jeśli odpowiedzi na zapytanie zawiera wiele elementów, usługa zwraca "\@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Nie można skonfigurować rozmiaru strony. 

Jeśli StreamingPolicy są tworzone lub usuwane podczas stronicować kolekcji, zmiany zostaną odzwierciedlone w zwróconych wyników, (Jeśli te zmiany w części w kolekcji, która nie została pobrana.) 

W poniższym przykładzie C# pokazano, jak wyliczyć za pośrednictwem wszystkich ContentKeyPolicies w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

POZOSTAŁE przykłady można znaleźć [zasady kluczy zawartości — lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="next-steps"></a>Kolejne kroki

[Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy](protect-with-aes128.md)

[Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
