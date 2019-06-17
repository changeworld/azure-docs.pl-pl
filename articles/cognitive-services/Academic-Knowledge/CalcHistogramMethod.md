---
title: Metoda CalcHistogram — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Metoda CalcHistogram do obliczania rozkładu wartości atrybutu dla zestawu jednostek papieru.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a228c5b90e47c9c24c5da70484a1a28f9a3054b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498836"
---
# <a name="calchistogram-method"></a>Metoda CalcHistogram

**Calchistogram** interfejsu API REST jest używane do obliczania rozkładu wartości atrybutu dla zestawu jednostek papieru.          


**Punkt końcowy REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa)  |Wartość | Wymagana?  |Opis
-----------|----------|--------|----------
**expr**    |Ciąg tekstowy | Tak  |Wyrażenie zapytania, które określa jednostki, względem którego ma zostać obliczanie histogramów.
**model** |Ciąg tekstowy | Nie |Wybierz nazwę modelu, który chcesz zbadać.  Obecnie ma domyślnie wartość *najnowsze*.
**Atrybuty** | Ciąg tekstowy | Nie<br>Wartość domyślna: | Listę rozdzielonych przecinkami, która określa wartości atrybutów, które znajdują się w odpowiedzi. Nazwy atrybutów jest rozróżniana wielkość liter.
**count** |Liczba | Nie<br>Wartość domyślna: 10 |Liczba wyników do zwrócenia.
**offset**  |Liczba | Nie<br>Wartość domyślna: 0 |Indeks pierwszego wyniku do zwrócenia.
**limit czasu**  |Liczba | Nie<br>Wartość domyślna: 1000 |Przekroczono limit czasu w milisekundach. Zwracane są tylko interpretacji znaleziono przed upływem limitu czasu.

## <a name="response-json"></a>Odpowiedź (JSON)

Name (Nazwa) | Opis
--------|---------
**expr**  |Parametr wyrażenie z żądania.
**num_entities** | Łączna liczba zgodnych jednostek.
**histograms** |  Tablica histogramów, jeden dla każdego atrybutu określona w żądaniu.
**histograms[x].attribute** | Nazwa atrybutu, względem której została obliczona histogram.
**histograms[x].distinct_values** | Liczba unikatowych wartości między dopasowania jednostek dla tego atrybutu.
**histograms[x].total_count** | Całkowita liczba wystąpień wartość między dopasowania jednostek dla tego atrybutu.
**histograms[x].histogram** | Histogram dane dla tego atrybutu.
**histograms[x].histogram[y].value** |  Wartość atrybutu.
**histograms[x].histogram[y].logprob**  |Łączna liczba logarytmu naturalnego prawdopodobieństwo, że dopasowanie jednostki z wartością tego atrybutu.
**histograms[x].histogram[y].count**  |Liczba zgodne jednostki z wartością tego atrybutu.
**aborted** | Wartość true, jeśli upłynął limit czasu żądania.


#### <a name="example"></a>Przykład:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>W tym przykładzie, aby wygenerować histogram liczby publikacje według roku dla danego autora od 2010, firma Microsoft może najpierw wygenerować za pomocą wyrażenia zapytania **interpretacji** interfejsu API za pomocą ciągu zapytania: *papers przez teevan trzyczęściową po 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Wyrażenie, które w pierwszej interpretacji, który jest zwracany z interpretację interfejsu API jest *i (złożonego (AA. AuN == "trzyczęściową teevan"), Y > 2012)* .
<br>Ta wartość wyrażenia jest następnie przekazywany do **calchistogram** interfejsu API. *Attributes=Y,F.FN* parametr wskazuje, że dystrybucje liczby dokument powinny być według roku i pola badania, np.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Odpowiedź na to żądanie najpierw oznacza, że 37 dokumentów, które pasują do wyrażenia zapytania.  Aby uzyskać *roku* atrybutu, istnieją 3 różne wartości, po jednym dla każdego roku po 2012 (czyli 2013, 2014 i 2015), jak określono w zapytaniu.  Liczba całkowita dokument za pośrednictwem 3 różne wartości jest 37.  Dla każdego *roku*, histogram pokazuje wartości, prawdopodobieństwa całkowita logarytmu naturalnego oraz liczby pasujących jednostek.     

Histogram dla *pola badanie* wskazuje, że są 34 unikatowych pól studiów. Jak dokument może być skojarzone z wieloma polami studiów, łączna liczba (53) może być większa niż liczba zgodnych jednostek.  Mimo że istnieją 34 różne wartości, odpowiedź zawiera tylko pierwszych 4 z powodu *count = 4* parametru.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
