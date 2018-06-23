---
title: Metoda CalcHistogram w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Metoda CalcHistogram do obliczenia rozkład wartości atrybutów dla zestawu jednostek papieru w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e0b773fb9791ee638c8cfdbbc9dca40543e50ec0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346901"
---
# <a name="calchistogram-method"></a>CalcHistogram — metoda

**Calchistogram** interfejsu API REST jest używane do obliczania rozkład wartości atrybutów dla zestawu jednostek papieru.          


**Punkt końcowy REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa)  |Wartość | Wymagana?  |Opis
-----------|----------|--------|----------
**expr**    |Ciąg tekstowy | Yes  |Wyrażenia zapytania, które określa jednostek służącym do obliczenia histogramów.
**Model** |Ciąg tekstowy | Nie |Wybierz nazwę modelu, który chcesz zbadać.  Obecnie, domyślnie przyjmowana jest wartość *najnowsze*.
**Atrybuty** | Ciąg tekstowy | Nie<br>Wartość domyślna: | Rozdzielana przecinkami lista określająca wartości atrybutów, które znajdują się w odpowiedzi. W nazwach atrybutów jest uwzględniana wielkość liter.
**Liczba** |Liczba | Nie<br>Domyślny: 10 |Liczba wyników do zwrócenia.
**offset**  |Liczba | Nie<br>Domyślna: 0 |Indeks pierwszego wyniku do zwrócenia.
<br>
## <a name="response-json"></a>Odpowiedź (JSON)
Name (Nazwa) | Opis
--------|---------
**expr**  |Wyrażenie parametru z żądaniem.
**num_entities** | Całkowita liczba zgodnych jednostek.
**histogramów** |  Tablica histogramów, po jednej dla każdego atrybutu określony w żądaniu.
**.attribute histogramów [x]** | Nazwa atrybutu, w którym została obliczona histogramu.
**.distinct_values histogramów [x]** | Liczba wartości odrębnych wśród odpowiadającym jednostek dla tego atrybutu.
**.total_count histogramów [x]** | Całkowita liczba wystąpień wartość wśród odpowiadającym jednostek dla tego atrybutu.
**.histogram histogramów [x]** | Histogram dane dla tego atrybutu.
**histogramów [.value .histogram [y] x]** |  Wartość atrybutu.
**histogramów [.logprob .histogram [y] x]**  |Całkowita liczba logarytm naturalny prawdopodobieństwo zgodnych jednostek z wartością tego atrybutu.
**histogramów [.count .histogram [y] x]**  |Liczba zgodnych jednostek z wartością tego atrybutu.
**zostało przerwane** | Wartość true, jeśli upłynął limit czasu żądania.

 <br>
#### <a name="example"></a>Przykład:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>W tym przykładzie, aby wygenerować histogram liczby publikacji roku dla konkretnego autora od 2010, firma Microsoft może najpierw wygenerować za pomocą wyrażenia zapytania **interpretowania** interfejsu API za pomocą ciągu zapytania: *dokumenty przez teevan jaime po 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Wyrażenie w pierwszym interpretację jest zwracana z interpret interfejsu API jest *i (złożone (AA. AuN == "jaime teevan"), Y > 2012)*.
<br>Wartość tego wyrażenia są następnie przekazywane w celu **calchistogram** interfejsu API. *Attributes=Y,F.FN* parametr wskazuje, czy dystrybucje liczby papieru powinna być roku i pola badania, np.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Odpowiedzi na to żądanie najpierw wskazuje, czy 37 dokumenty, które pasują do wyrażenia zapytania.  Aby uzyskać *roku* atrybutu, istnieją 3 różne wartości, po jednej dla każdego roku po 2012 (tj. 2013, 2014 i 2015) określone w zapytaniu.  Liczba całkowita papieru przez 3 różne wartości jest 37.  Dla każdego *roku*, histogram zawiera wartość prawdopodobieństwa całkowita logarytm naturalny, a liczba zgodnych jednostek.     

Histogramu *pola badać* pokazuje są 34 różne pola badania. Dokument może być skojarzony z wielu pól badań, łączna liczba (53) może być większa niż liczba zgodnych jednostek.  Mimo że istnieją różne wartości 34, odpowiedź zawiera tylko pierwszych 4 z powodu *count = 4* parametru.

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
