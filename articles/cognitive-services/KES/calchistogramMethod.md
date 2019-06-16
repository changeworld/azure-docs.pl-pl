---
title: Metoda CalcHistogram — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak użyć metody CalcHistogram w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: aaa5b3a85c08f11d821557257de451b8ffc8a3fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814169"
---
# <a name="calchistogram-method"></a>calchistogram — metoda
*Calchistogram* metoda oblicza obiektów dopasowywania wyrażeń zapytań ze strukturą i oblicza rozkład wartości atrybutu.

## <a name="request"></a>Żądanie
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name (Nazwa)|Wartość|Opis
----|-----|-----------
expr | Ciąg tekstowy | Wyrażenia zapytań ze strukturą, które określa jednostki indeksu, względem którego ma zostać obliczanie histogramów.
Atrybuty | Ciąg tekstowy (domyślny = "") | Rozdzielana przecinkami lista atrybutów do dołączenia w odpowiedzi.
count   | Liczba (domyślny = 10) | Liczba wyników do zwrócenia.
offset  | Liczba (domyślny = 0) | Indeks pierwszego wyniku do zwrócenia.

## <a name="response-json"></a>Odpowiedź (JSON)
JSONPath | Opis
----|----
$.expr | *wyrażenie* parametrów z żądania.
$.num_entities | Łączna liczba zgodnych jednostek.
$.histograms |  Tablica histogramów, jeden dla każdego żądanego atrybutu.
$.histograms[\*].attribute | Nazwa atrybutu, względem której została obliczona histogram.
$.histograms[\*].distinct_values | Liczba unikatowych wartości między dopasowania jednostek dla tego atrybutu.
$.histograms[\*].total_count | Całkowita liczba wystąpień wartość między dopasowania jednostek dla tego atrybutu.
$.histograms[\*].histogram | Histogram dane dla tego atrybutu.
$.histograms[\*].histogram[\*].value | Wartość atrybutu.
$.histograms[\*].histogram[\*].logprob  | Łączna liczba logarytmu naturalnego prawdopodobieństwo, że dopasowanie jednostki z wartością tego atrybutu.
$.histograms[\*].histogram[\*].count    | Liczba zgodne jednostki z wartością tego atrybutu.
$.aborted | Wartość true, jeśli upłynął limit czasu żądania.

### <a name="example"></a>Przykład
W tym przykładzie akademickich publikacje następujące oblicza histogram liczby publikacji przez rok i słowo kluczowe dla danego autora od 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Odpowiedź wskazuje, że 37 dokumentów dopasowania wyrażenia zapytania.  Aby uzyskać *roku* atrybutu, istnieją 3 różne wartości, jeden dla każdego roku od 2013 r.  Liczba całkowita dokument za pośrednictwem 3 różne wartości jest 37.  Dla każdego *roku*, histogram pokazuje wartości, prawdopodobieństwa całkowita logarytmu naturalnego oraz liczby pasujących jednostek.     

Histogram dla *— słowo kluczowe* wskazuje, że są 34 unikatowych słów kluczowych. Jak dokument może być skojarzony z wieloma słów kluczowych, łączna liczba (53) może być większa niż liczba zgodnych jednostek.  Mimo że istnieją 34 różne wartości, odpowiedź zawiera tylko pierwszych 4 z powodu "liczba = 4" parametru.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
