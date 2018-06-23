---
title: Interpretowanie metoda w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć tej metody Interpret w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: ef68d98dacf393abf8d030b9312217ea380947d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347093"
---
# <a name="interpret-method"></a>Interpretowanie — metoda
*Interpretowania* metoda pobiera ciąg zapytania języka naturalnego i zwraca sformatowany interpretacje zamiarów użytkownika na podstawie danych gramatyki i indeksu.  Aby zapewnić środowisko wyszukiwania interaktywne, ta metoda może zostać wywołana wprowadzone przez użytkownika z każdego znaku *pełną* ustawiona na 1 Włącz sugestie automatycznego zakończenia.

## <a name="request"></a>Żądanie
`http://<host>/interpret?query=<query>[&<options>]`

Name (Nazwa)|Wartość| Opis
----|----|----
query    | Ciąg tekstowy | Zapytanie wprowadzonej przez użytkownika.  Pełne jest ustawiona na 1, kwerendy będą interpretowane jako prefiksu do generowania sugestie automatycznego uzupełniania zapytania.        
Zakończenie | 0 (ustawienie domyślne) lub 1 | 1 oznacza, że sugestie automatycznego uzupełniania są generowane, na podstawie danych gramatyki i indeksu.         
count    | Numer (domyślne = 10) | Maksymalna liczba interpretacje do zwrócenia.         
Przesunięcie   | Numer (domyślne = 0) | Indeks pierwszego interpretacji do zwrócenia.  Na przykład *count = 2 & przesunięcie = 0* zwraca interpretacje 0 i 1. *liczba = 2 & przesunięcie = 2* zwraca interpretacje 2 i 3.       
timeout  | Numer (domyślne = 1000) | Limit czasu w milisekundach. Zwracane są tylko interpretacje znaleziono przed upływem limitu czasu.

Przy użyciu *liczba* i *przesunięcie* parametrów dużej liczby wyników można uzyskać przyrostowo przez wiele żądań.

## <a name="response-json"></a>Odpowiedź (JSON)
JSONPath     | Opis
---------|---------
$.query |*Zapytanie* parametrów z żądania.
$.interpretations   |Tablica 0 lub więcej sposobów pasuje wejściowe zapytania dotyczącego gramatyki.
$.interpretations [\*] .logprob   |Względne dziennika prawdopodobieństwo interpretacji (< = 0).  Wyższe wartości są bardziej prawdopodobne.
$.interpretations [\*] .parse |Ciąg XML, który zawiera interpretacji każdej części zapytania.
$.interpretations [\*] Rules |Tablica 1 lub więcej reguł zdefiniowanych w gramatyce wywoływane podczas interpretacji.
$.interpretations [\*] Rules [\*] .Nazwa    |Nazwa reguły.
$.interpretations [\*] Rules [\*] .output  |Semantycznego dane wyjściowe reguły.
$.interpretations [\*] Rules [\*]. output.type |Typ danych wyjściowych semantycznego.
$.interpretations [\*] Rules [\*]. output.value|Wartość semantyki danych wyjściowych.  
$.aborted | Wartość true, jeśli upłynął limit czasu żądania.

### <a name="parse-xml"></a>Dokonać analizy pliku XML
Analizy XML oznacza (ukończone) zapytania z informacjami o jak odpowiada względem reguły w gramatyce i atrybutów w indeksie.  Poniżej znajduje się przykład z publikacji academic domeny:

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

`<rule>` Element rozgranicza zakresu w zapytaniu dopasowywania reguły określony przez jego `name` atrybutu.  Mogą być zagnieżdżone, gdy analizy obejmuje odwołania do reguły w gramatyce.

`<attr>` Element rozgranicza zakresu w zapytaniu dopasowania określony przez atrybut indeksu jego `name` atrybutu.  Gdy dopasowania obejmuje synonim w zapytanie wejściowe `canonical` atrybut będzie zawierał wartości kanonicznej dopasowania synonim z indeksu.

## <a name="example"></a>Przykład
W przykładzie academic publikacji następujące żądania zwraca maksymalnie 2 automatycznego uzupełniania sugestie dotyczące zapytań prefiksu "dokumentach jaime":

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Odpowiedź zawiera dwóch pierwszych ("count = 2") najprawdopodobniej interpretacje, kończące z częściowa zapytania "dokumentach jaime": "dokumenty przez jaime teevan" i "dokumenty przez zielony jaime".  Zakończeń zapytania usługi wygenerowany zamiast uwzględnieniu tylko dokładne odpowiedniki Autor "jaime", ponieważ określony w żądaniu "pełną = 1". Należy pamiętać, że wartości kanonicznej "zielony j l" dopasowane za pośrednictwem synonim "jamie zielony", zgodnie z analizy.


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

Po typ danych wyjściowych semantycznego to "query", jak w poniższym przykładzie zgodnych obiektów mogą być pobierane przez przekazanie *output.value* do [ *oceny* ](evaluateMethod.md) interfejsu API za pomocą *wyrażenie* parametru.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
