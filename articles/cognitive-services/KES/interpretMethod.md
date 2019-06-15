---
title: Interpretowanie metody — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak użyć tej metody interpretację w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 88776e2f4167c950d60c0405dcf950b5173fb989
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814138"
---
# <a name="interpret-method"></a>Interpretowanie — metoda

*Interpretacji* metoda przyjmuje ciąg zapytania w języku naturalnym i zwraca sformatowany interpretacji intencji użytkownika na podstawie danych Gramatyka języka i indeksem.  W celu udostępnienia środowiska interaktywnego wyszukiwania, może można wywołać tej metody wprowadzone przez użytkownika za pomocą każdego znaku *pełną* parametru równa 1, aby włączyć automatyczne uzupełnianie sugestie.

## <a name="request"></a>Żądanie

`http://<host>/interpret?query=<query>[&<options>]`

Name (Nazwa)|Wartość| Opis
----|----|----
query    | Ciąg tekstowy | Zapytanie wprowadzonej przez użytkownika.  Pełne jest ustawiona na 1, zapytania będą interpretowane jako prefiks dla generowania sugestie automatyczne uzupełnianie zapytań.        
Wykonaj | 0 (ustawienie domyślne) lub 1 | 1 oznacza, że wygenerowany automatycznego uzupełniania, sugestii na podstawie danych Gramatyka języka i indeksem.         
count    | Liczba (domyślny = 10) | Maksymalna liczba interpretacji do zwrócenia.         
offset   | Liczba (domyślny = 0) | Indeks pierwszego interpretacji do zwrócenia.  Na przykład *count = 2 & przesunięcie = 0* zwraca interpretacje 0 i 1. *liczba = 2 & przesunięcie = 2* zwraca interpretacje 2 i 3.       
timeout  | Liczba (domyślny = 1000) | Przekroczono limit czasu w milisekundach. Zwracane są tylko interpretacji znaleziono przed upływem limitu czasu.

Za pomocą *liczba* i *przesunięcie* parametrów, dużą liczbę wyników można uzyskać przyrostowo za pośrednictwem wielu żądań.

## <a name="response-json"></a>Odpowiedź (JSON)

JSONPath     | Opis
---------|---------
$.query |*Zapytanie* parametrów z żądania.
$.interpretations   |Tablica 0 lub więcej sposobów, aby dopasować wejściowych zapytania dotyczącego gramatyki.
$.interpretations[\*].logprob   |Względna dziennika prawdopodobieństwo interpretacji (< = 0).  Większe wartości są bardziej prawdopodobne.
$.interpretations[\*].parse |Ciąg XML, który pokazuje, jak interpretować została każda część zapytania.
$.interpretations [\*] Rules |Tablica 1 lub więcej reguł zdefiniowanych w gramatyce wywoływane podczas interpretacji.
$.interpretations [\*] Rules [\*] .name    |Nazwa reguły.
$.interpretations [\*] Rules [\*] .output  |Semantyczne dane wyjściowe reguły.
$.interpretations [\*] Rules [\*]. output.type |Typ danych semantycznych danych wyjściowych.
$.interpretations [\*] Rules [\*]. output.value|Wartość semantycznego danych wyjściowych.  
$.aborted | Wartość true, jeśli upłynął limit czasu żądania.

### <a name="parse-xml"></a>Analizowanie danych XML

Analizy XML oznacza stosowanym (ukończenie) zapytanie o informacje jak dopasowuje względem reguły w gramatyce i atrybuty w indeksie.  Poniżej przedstawiono przykład z domeny akademickich publikacji:

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

`<rule>` Element rozgranicza zakresu w zapytaniu pasujących reguł, określony przez jego `name` atrybutu.  Może być zagnieżdżony, gdy analizy obejmuje odwołania do reguły w gramatyce.

`<attr>` Element rozgranicza zakresu w zapytaniu dopasowanie atrybutu indeksu, określony przez jego `name` atrybutu.  Gdy dopasowanie obejmuje synonimów w zapytanie wejściowe `canonical` atrybut będzie zawierać wartość canonical dopasowania synonimu z indeksu.

## <a name="example"></a>Przykład

W tym przykładzie akademickich publikacje następujące żądanie zwraca maksymalnie 2 sugestie automatycznego uzupełniania dla zapytania prefiksu "dokumentach trzyczęściową":

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Odpowiedź zawiera dwa pierwsze ("liczba = 2") najprawdopodobniej interpretacji, które wykonać zapytania częściowego "dokumentach trzyczęściową": "papers przez teevan trzyczęściową" i "papers przez zielony trzyczęściową".  Uzupełnienia zapytań usługi generowane zamiast biorąc pod uwagę tylko dokładne dopasowania dla autora "trzyczęściową", ponieważ określony w żądaniu "Pełna = 1". Należy pamiętać, że wartość canonical "l" j"zielony" dopasowane za pośrednictwem synonim "jamie green", jak wskazano w analizy.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Jeśli typ danych semantycznych wyjściowych jest "query", jak w poniższym przykładzie, zgodnych obiektów mogą być pobierane przez przekazanie *output.value* do [ *oceny* ](evaluateMethod.md) interfejsu API za pośrednictwem *expr* parametru.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
