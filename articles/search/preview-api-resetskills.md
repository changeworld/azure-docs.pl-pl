---
title: Resetowanie umiejętności (API-Version = 2019-050 -06 — wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Interfejs API REST w wersji zapoznawczej służący do wzbogacania przyrostowego, gdy konieczne jest całkowite lub częściowe ponowne przetworzenie zestawu umiejętności.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832153"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Resetowanie umiejętności (API-Version = 2019-05 -06 — wersja zapoznawcza)

> [!IMPORTANT] 
> Wzbogacanie przyrostowe jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

Żądanie **resetowania umiejętności** określa, które umiejętności mają być przetwarzane na następnym uruchomieniu indeksatora. W przypadku indeksatorów z włączonym buforowaniem można jawnie zażądać przetwarzania aktualizacji umiejętności, których indeksator nie może wykryć. Jeśli na przykład wprowadzisz zmiany zewnętrzne, takie jak poprawki do niestandardowej umiejętności, możesz użyć tego interfejsu API, aby ponownie uruchomić umiejętność. Dane wyjściowe, takie jak magazyn wiedzy lub indeks wyszukiwania, są odświeżane przy użyciu danych wielokrotnego użytku z pamięci podręcznej i nowej zawartości na zaktualizowane umiejętności.

Istniejące [zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) można zresetować przy użyciu funkcji Put http, określając nazwę zestawu umiejętności do zaktualizowania w identyfikatorze URI żądania. Musisz użyć **interfejsu API-Version = 2019-05 -06 — wersja zapoznawcza** na żądanie.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

Alternatywnie możesz użyć pozycji POST i umieścić nazwę indeksatora w treści żądania:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Nagłówki żądań  

 W poniższej tabeli opisano wymagane i opcjonalne nagłówki żądań.  

|Nagłówek żądania|Opis|  
|--------------------|-----------------|  
|*Content-Type:*|Wymagany. Ustaw tę wartość na `application/json`|  
|*klucz API-Key:*|Wymagany. `api-key` służy do uwierzytelniania żądania do usługi wyszukiwania. Jest to wartość typu ciąg, która jest unikatowa dla usługi. Żądanie **resetowania zestawu umiejętności** musi zawierać nagłówek `api-key` ustawiony na klucz administratora (w przeciwieństwie do klucza zapytania).|  

Potrzebna jest również nazwa usługi do konstruowania adresu URL żądania. W Azure Portal można uzyskać zarówno nazwę usługi, jak i `api-key` na stronie przeglądu usługi. Zobacz [Tworzenie usługi Wyszukiwanie poznawcze Azure,](https://docs.microsoft.com/azure/search/search-create-service-portal) Aby uzyskać pomoc dotyczącą nawigacji na stronie.  

## <a name="request-body-syntax"></a>Składnia treści żądania  

Treść żądania jest tablicą nazw umiejętności.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Odpowiedź  

Kod stanu: 204 Brak zawartości dla pomyślnej odpowiedzi. 

## <a name="see-also"></a>Zobacz także

+ [Wyszukaj interfejsy API REST](https://docs.microsoft.com/rest/api/searchservice)
+ [Kody stanu HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Przegląd wzbogacania AI](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Konfigurowanie buforowania i przyrostowego wzbogacania](search-howto-incremental-index.md)
+ [Wzbogacanie przyrostowe](cognitive-search-incremental-indexing-conceptual.md)