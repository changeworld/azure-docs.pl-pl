---
title: Metoda CalcHistogram w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć tej metody CalcHistogram w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 6ed694b0cc9cf41b815cc54b9f6d12adb2b7cd64
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347033"
---
# <a name="calchistogram-method"></a>calchistogram — metoda
*Calchistogram* metody oblicza obiektów zgodnego z wyrażeniem structured query i oblicza rozkład wartości atrybutu.

## <a name="request"></a>Żądanie
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name (Nazwa)|Wartość|Opis
----|-----|-----------
wyrażenie | Ciąg tekstowy | Wyrażenie Structured query jednostek indeksem służącym do obliczenia histogramów.
Atrybuty | Ciąg tekstowy (domyślne = "") | Rozdzielana przecinkami lista atrybutów do dołączenia w odpowiedzi.
count   | Numer (domyślne = 10) | Liczba wyników do zwrócenia.
Przesunięcie  | Numer (domyślne = 0) | Indeks pierwszego wyniku do zwrócenia.

## <a name="response-json"></a>Odpowiedź (JSON)
JSONPath | Opis
----|----
$.expr | *wyrażenie* parametrów z żądania.
$.num_entities | Całkowita liczba zgodnych jednostek.
$.histograms |  Tablica histogramów, jeden dla każdej żądanej właściwości.
$.histograms [\*] .attribute | Nazwa atrybutu, w którym została obliczona histogramu.
$.histograms [\*] .distinct_values | Liczba wartości odrębnych wśród odpowiadającym jednostek dla tego atrybutu.
$.histograms [\*] .total_count | Całkowita liczba wystąpień wartość wśród odpowiadającym jednostek dla tego atrybutu.
$.histograms [\*] .histogram | Histogram dane dla tego atrybutu.
$.histograms [\*] .histogram [\*] .value | Wartość atrybutu.
$.histograms [\*] .histogram [\*] .logprob  | Całkowita liczba logarytm naturalny prawdopodobieństwo zgodnych jednostek z wartością tego atrybutu.
$.histograms [\*] .histogram [\*] .count    | Liczba zgodnych jednostek z wartością tego atrybutu.
$.aborted | Wartość true, jeśli upłynął limit czasu żądania.

### <a name="example"></a>Przykład
W przykładzie academic publikacji następujące oblicza histogram publikacji liczników za pomocą roku i słowo kluczowe dla konkretnego autora od 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Odpowiedź wskazuje, czy dokumenty 37 dopasowanie wyrażenia zapytania.  Aby uzyskać *roku* atrybutu, istnieją 3 różne wartości, po jednej dla każdego roku od 2013.  Liczba całkowita papieru przez 3 różne wartości jest 37.  Dla każdego *roku*, histogram zawiera wartość prawdopodobieństwa całkowita logarytm naturalny, a liczba zgodnych jednostek.     

Histogramu *— słowo kluczowe* pokazuje, czy 34 słowa kluczowe distinct. Dokument może być skojarzony z wieloma słowami kluczowymi, łączna liczba (53) może być większa niż liczba zgodnych jednostek.  Mimo że istnieją różne wartości 34, odpowiedź zawiera tylko pierwszych 4 z powodu "count = 4" parametru.

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
