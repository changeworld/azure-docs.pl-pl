---
title: Metoda CalcHistogram — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Użyj metody CalcHistogram, aby obliczyć rozkład wartości atrybutów dla zestawu jednostek papieru.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 9e84b1ad37b3224ec5553d0a66ba0fc84bc88f55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705082"
---
# <a name="calchistogram-method"></a>CalcHistogram, Metoda

Interfejs API REST **calchistogram** służy do obliczania rozkładu wartości atrybutów dla zestawu jednostek papieru.          


**Punkt końcowy REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa)  |Value | Wymagane?  |Opis
-----------|----------|--------|----------
**expr**    |Ciąg tekstowy | Yes  |Wyrażenie zapytania, które określa jednostki, dla których należy obliczyć histogramy.
**model** |Ciąg tekstowy | Nie |Wybierz nazwę modelu, który chcesz zbadać.  Obecnie wartość domyślna to *Najnowsza*.
**Attributes** | Ciąg tekstowy | Nie<br>wartooć | Rozdzielana przecinkami lista określająca wartości atrybutów, które są zawarte w odpowiedzi. W nazwach atrybutów jest rozróżniana wielkość liter.
**count** |Number | Nie<br>Domyślne: 10 |Liczba wyników do zwrócenia.
**offset**  |Number | Nie<br>Domyślne: 0 |Indeks pierwszego wyniku do zwrócenia.
**limit czasu**  |Number | Nie<br>Domyślne: 1000 |Limit czasu (w milisekundach). Zwracane są tylko interpretacje znalezione przed upływem limitu czasu.

## <a name="response-json"></a>Odpowiedź (JSON)

Name (Nazwa) | Opis
--------|---------
**expr**  |Parametr Expr z żądania.
**num_entities** | Łączna Liczba pasujących jednostek.
**histograms** |  Tablica histogramów, jeden dla każdego atrybutu określonego w żądaniu.
**histograms[x].attribute** | Nazwa atrybutu, w którym histogram został obliczony.
**histograms[x].distinct_values** | Liczba różnych wartości między zgodnymi jednostkami dla tego atrybutu.
**histogramy [x]. total_count** | Łączna liczba wystąpień wartości między zgodnymi jednostkami dla tego atrybutu.
**histograms[x].histogram** | Dane histogramu dla tego atrybutu.
**histograms[x].histogram[y].value** |  Wartość atrybutu.
**histograms[x].histogram[y].logprob**  |Łączne prawdopodobieństwo związane z logowaniem do odpowiednich jednostek z tą wartością atrybutu.
**histograms[x].histogram[y].count**  |Liczba zgodnych jednostek z tą wartością atrybutu.
**aborted** | Ma wartość true, jeśli upłynął limit czasu żądania.


#### <a name="example"></a>Przykład:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>W tym przykładzie w celu wygenerowania histogramu liczby publikacji według roku dla danego autora od 2010, możemy najpierw wygenerować wyrażenie zapytania przy użyciu interpretera API z ciągiem zapytania: *dokumenty przez jaime teevan po 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Wyrażenie w pierwszej interpretacji, które jest zwracane z interpretera API, to *i (Composite (AA. AuN = = ' Jaime teevan '), Y > 2012)* .
<br>Ta wartość wyrażenia jest następnie przenoszona do interfejsu API **calchistogram** . *Atrybuty = Y, F. Fn* wskazują, że rozkłady liczby papieru powinny należeć do roku i pola analizy, np.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Odpowiedź na to żądanie najpierw wskazuje, że istnieją 37 dokumenty zgodne z wyrażeniem zapytania.  Dla atrybutu *Year* istnieją 3 odrębne wartości, jeden dla każdego roku po 2012 (tj. 2013, 2014 i 2015) określony w zapytaniu.  Łączna liczba papierów przez 3 różne wartości to 37.  W każdym *roku*histogram pokazuje wartość, łączne prawdopodobieństwo w dzienniku naturalnym oraz liczbę pasujących jednostek.     

Histogram dla *pola badania* pokazuje, 34 że istnieją różne pola analizy. Ponieważ dokument może być skojarzony z wieloma polami badań, Łączna liczba (53) może być większa niż liczba pasujących jednostek.  Chociaż istnieją różne wartości w liczbie 34, odpowiedź obejmuje tylko pierwsze 4 z powodu liczby *= 4* parametrów.

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
