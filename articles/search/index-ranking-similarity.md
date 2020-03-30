---
title: Algorytm podobieństwa rankingu
titleSuffix: Azure Cognitive Search
description: Jak ustawić algorytm podobieństwa, aby wypróbować nowy algorytm podobieństwa do rankingu
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409975"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algorytm rankingowy w usłudze Azure Cognitive Search

> [!IMPORTANT]
> Od 15 lipca 2020 r. nowo utworzone usługi wyszukiwania będą korzystać z funkcji rankingu BM25, która w większości przypadków udowodniła, że zapewnia rankingi wyszukiwania, które lepiej dostosowują się do oczekiwań użytkowników niż obecny domyślny ranking.  Poza doskonałą klasyfikacją, BM25 umożliwia również opcje konfiguracji dla dostrajania wyników na podstawie czynników, takich jak rozmiar dokumentu.  
>
> Dzięki tej zmianie najprawdopodobniej zobaczysz niewielkie zmiany w kolejności wyników wyszukiwania.   Dla tych, którzy chcą przetestować wpływ tej zmiany, udostępniliśmy w interfejsie API 2019-05-06-Preview możliwość umożliwienia oceniania BM25 na nowe indeksy.  

W tym artykule opisano, jak można zaktualizować usługę utworzoną przed 15 lipca 2020 r., aby użyć nowego algorytmu klasyfikacji BM25.

Usługa Azure Cognitive Search będzie używać oficjalnej implementacji Lucene algorytmu Okapi BM25, *BM25Similarity*, który zastąpi wcześniej używaną implementację *ClassicSimilarity.* Podobnie jak starszy algorytm ClassicSimilarity, BM25Podobność jest TF-IDF-jak funkcja pobierania, która używa terminu częstotliwość (TF) i odwrotnej częstotliwości dokumentu (IDF) jako zmienne do obliczania wyników trafności dla każdej pary dokumentu zapytania, który jest następnie używane do rankingu. Podczas koncepcyjnie podobny do starszego algorytmu podobieństwa klasycznego, BM25 ma swój korzeń w probabilistycznych informacji pobierania, aby poprawić na nim. BM25 oferuje również zaawansowane opcje dostosowywania, takie jak umożliwienie użytkownikowi decydowania o tym, jak trafność ocenia skaluje się z terminem częstotliwość dopasowanych terminów.

## <a name="how-to-test-bm25-today"></a>Jak przetestować BM25 już dziś

Podczas tworzenia nowego indeksu można ustawić właściwość "podobieństwo". Musisz użyć wersji *2019-05-06-Preview,* jak pokazano poniżej.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

W przypadku usług utworzonych przed 15 lipca 2020 r.: Jeśli podobieństwo zostanie pominięte lub ustawione na wartość null, indeks użyje starego klasycznego algorytmu podobieństwa.

Dla usług utworzonych po 15 lipca 2020 r.: Jeśli podobieństwo zostanie pominięte lub ustawione na wartość null, indeks użyje nowego algorytmu podobieństwa BM25.

Można również jawnie ustawić wartość podobieństwa jako jedną z następujących dwóch wartości: *"#Microsoft.Azure.Search.ClassicSimilarity"* lub *"#Microsoft.Azure.Search.BM25Podobność"*.


## <a name="bm25-similarity-parameters"></a>Parametry podobieństwa BM25

Podobieństwo BM25 dodaje dwa parametry konfigurowalne przez użytkownika, aby kontrolować obliczony wynik trafności:

### <a name="k1"></a>k1

Parametr *k1* steruje funkcją skalowania między częstotliwością określania terminu każdego pasującego terminu a końcowym wynikiem trafności pary zapytań dokumentu.

Wartość zero reprezentuje "model binarny", w którym udział pojedynczego pasującego terminu jest taki sam dla wszystkich pasujących dokumentów, niezależnie od tego, ile razy ten termin pojawia się w tekście, podczas gdy większa wartość k1 pozwala na dalszy wzrost wyniku w miarę większej liczby wystąpienia tego samego terminu znajduje się w dokumencie. Domyślnie usługa Azure Cognitive Search używa wartości 1,2 dla parametru k1. Przy użyciu wyższej wartości k1 może być ważne w przypadkach, gdy oczekujemy, że wiele terminów będzie częścią zapytania wyszukiwania. W takich przypadkach możemy chcieć faworyzować dokumenty, które pasują do wielu różnych terminów zapytań przeszukiwanych przez dokumenty, które pasują tylko do jednego, wiele razy. Na przykład, podczaspytowania indeksu dla dokumentów zawierających terminy "Apollo Spaceflight", możemy chcieć obniżyć wynik artykułu o mitologii greckiej, który zawiera termin "Apollo" kilkadziesiąt razy, bez wzmianki o "Spaceflight", w porównaniu do inny artykuł, który wyraźnie wspomina zarówno "Apollo" i "Spaceflight" tylko kilka razy. 
 
### <a name="b"></a>b

Parametr *b* określa, jak długość dokumentu wpływa na wynik trafności.

Wartość 0,0 oznacza, że długość dokumentu nie wpłynie na wynik, podczas gdy wartość 1,0 oznacza, że wpływ częstotliwości terminów na wynik trafności zostanie znormalizowany przez długość dokumentu. Wartość domyślna używana w usłudze Azure Cognitive Search dla parametru b to 0.75. Normalizacja terminu częstotliwość przez długość dokumentu jest przydatna w przypadkach, gdy chcemy karać dłuższe dokumenty. W niektórych przypadkach dłuższe dokumenty (takie jak pełna powieść) częściej zawierają wiele nieistotnych terminów w porównaniu z znacznie krótszymi dokumentami.

### <a name="setting-k1-and-b-parameters"></a>Ustawianie parametrów k1 i b

Aby dostosować wartości b lub k1, wystarczy dodać je jako właściwości do obiektu podobieństwa podczas korzystania z BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Algorytm podobieństwa można ustawić tylko w czasie tworzenia indeksu. Oznacza to, że algorytm podobieństwa używane nie można zmienić dla istniejących indeksów. Parametry *"b"* i *"k1"* mogą być modyfikowane podczas aktualizowania istniejącej definicji indeksu, która używa BM25. Zmiana tych wartości w istniejącym indeksie spowoduje przełączenie indeksu w tryb offline przez co najmniej kilka sekund, powodując niepowodzenie żądań indeksowania i kwerend. Z tego powodu należy ustawić parametr "allowIndexDowntime=true" w ciągu zapytania żądania aktualizacji:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Zobacz też  

 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Dodawanie profili oceniania do indeksu](index-add-scoring-profiles.md)    
 [Tworzenie &#40;interfejsu API usługi Azure Cognitive Search REST&#41;&#40;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
