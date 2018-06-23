---
title: Metoda w interfejsie Academic Knowledge API interpretacji | Dokumentacja firmy Microsoft
description: Użyj metody Interpret, aby zwrócić sformatowany interpretacje ciągów zapytania użytkownika na podstawie danych wykresu Academic i Academic gramatyki w kognitywnych usług firmy Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a46c792f14fabf6562666d1067ef880bd505741f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346917"
---
# <a name="interpret-method"></a>Interpretowanie — metoda

**Interpretowania** interfejsu API REST przyjmuje użytkownik końcowy ciągu zapytania (tj. zapytanie wprowadzonej przez użytkownika aplikacji) i zwraca sformatowany interpretacje zamiarów użytkownika na podstawie danych wykresu Academic i Academic gramatyki.

Aby zapewnić funkcje interaktywne, tę metodę mogą wywoływać wielokrotnie, po każdym znaku wprowadzony przez użytkownika. W takim przypadku należy ustawić **pełną** parametr 1, aby włączyć sugestie automatycznego zakończenia. Jeśli aplikacja nie wymaga automatycznego uzupełniania, należy ustawić **pełną** parametru na wartość 0.

**Punkt końcowy REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa)     | Wartość | Wymagana?  | Opis
---------|---------|---------|---------
**zapytania**    | Ciąg tekstowy | Yes | Zapytanie wprowadzonej przez użytkownika.  Pełne jest ustawiona na 1, kwerendy będą interpretowane jako prefiksu do generowania sugestie automatycznego uzupełniania zapytania.        
**Model**    | Ciąg tekstowy | Nie  | Nazwa modelu, który chcesz zbadać.  Obecnie, domyślnie przyjmowana jest wartość *najnowsze*.        
**Zakończenie** | 0 lub 1 | Nie<br>domyślna: 0  | 1 oznacza, że sugestie automatycznego uzupełniania są generowane, na podstawie danych wykres i gramatyki.         
**Liczba**    | Liczba | Nie<br>domyślny: 10 | Maksymalna liczba interpretacje do zwrócenia.         
**offset**   | Liczba | Nie<br>domyślna: 0  | Indeks pierwszego interpretacji do zwrócenia. Na przykład *count = 2 & przesunięcie = 0* zwraca interpretacje 0 i 1. *liczba = 2 & przesunięcie = 2* zwraca interpretacje 2 i 3.       
**limit czasu**  | Liczba | Nie<br>domyślnego: 1000 | Limit czasu w milisekundach. Zwracane są tylko interpretacje znaleziono przed upływem limitu czasu.
<br>
  
## <a name="response-json"></a>Odpowiedź (JSON)
Name (Nazwa)     | Opis
---------|---------
**zapytania** |*Zapytania* parametrów z żądania.
**interpretacje** |Tablica 0 lub więcej różnych sposobów dopasowywania danych wejściowych użytkownika przed gramatyki.
**.logprob interpretacje [x]**  |Prawdopodobieństwo względną logarytm naturalny interpretacji. Większe wartości są bardziej prawdopodobne.
**.parse interpretacje [x]**  |Ciąg XML, który zawiera interpretacji każdej części zapytania.
**Rules interpretacje [x]**  |Tablica 1 lub więcej reguł zdefiniowanych w gramatyki, które zostały wywoływane podczas interpretacji. Academic Knowledge interfejsu API zawsze będzie reguł: 1.
**interpretacje [.Nazwa Rules [y] x]**  |Nazwa reguły.
**interpretacje [.output Rules [y] x]**  |Dane wyjściowe reguły.
**interpretacje [.output.type Rules [y] x]** |Typ danych wyjściowych reguły.  Academic Knowledge interfejsu API ta będzie zawsze równa "zapytania".
**interpretacje [.output.value Rules [y] x]**  |Dane wyjściowe reguły. Academic Knowledge interfejsu API to ciąg wyrażenia zapytania, które mogą zostać przekazane do metody evaluate i calchistogram.
**zostało przerwane** | Wartość true, jeśli upłynął limit czasu żądania.

<br>
#### <a name="example"></a>Przykład:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Odpowiedź poniżej zawiera dwóch pierwszych (ze względu na parametr *count = 2*) najprawdopodobniej interpretacje, które zakończenie danych wejściowych użytkownika z częściowa *dokumentach jaime*: *dokumentach jaime teevan*  i *dokumentów przez zielony jaime*.  Zakończeń zapytania usługi wygenerowany zamiast uwzględnieniu tylko dokładne odpowiedniki dla autora *jaime* , ponieważ określony w żądaniu *pełną = 1*. Należy pamiętać, że wartości kanonicznej *l j zielony* dopasowane za pośrednictwem synonim *zielony Joanna*, zgodnie z analizy.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Aby uzyskać wyniki jednostki interpretacji, użyj *output.value* z **zinterpretować** interfejsu API i że do przekazania **oceny** interfejsu API za pomocą *wyrażenie*  parametru. W tym przykładzie zapytanie interpretacji pierwszy jest: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 