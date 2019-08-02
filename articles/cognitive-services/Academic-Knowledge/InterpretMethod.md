---
title: Interpretacja metody-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Użyj metody interpretowania, aby zwracać sformatowane interpretacje ciągów zapytań użytkowników na podstawie danych grafu akademickiego i nauki naukowej w Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d960aff109e0eca70cb87463770620093e563f63
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706670"
---
# <a name="interpret-method"></a>Metoda interpretowania

Interfejs API REST interpretera Pobiera ciąg zapytania użytkownika końcowego (tj. zapytanie wprowadzone przez użytkownika aplikacji) i zwraca sformatowaną interpretację zamiaru użytkownika na podstawie danych grafu akademickiego i naukowej gramatyki.

Aby zapewnić interaktywne środowisko, można wywołać tę metodę wielokrotnie po każdym znaku wprowadzonym przez użytkownika. W takim przypadku należy ustawić **pełny** parametr na 1, aby włączyć sugestie Autouzupełniania. Jeśli aplikacja nie wymaga automatycznego uzupełniania, należy ustawić parametr **Complete** na wartość 0.

**Punkt końcowy REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parametry żądania

Name (Nazwa)     | Wartość | Wymagane?  | Opis
---------|---------|---------|---------
**query**    | Ciąg tekstowy | Tak | Zapytanie wprowadzone przez użytkownika.  Jeśli ustawiono wartość 1, zapytanie będzie interpretowane jako prefiks do generowania sugestii autouzupełniania zapytań.        
**model**    | Ciąg tekstowy | Nie  | Nazwa modelu, który ma być wysyłany do zapytania.  Obecnie wartość domyślna to *Najnowsza*.        
**wykonanie** | 0 lub 1 | Nie<br>wartość domyślna: 0  | 1 oznacza, że sugestie Autouzupełniania są generowane na podstawie danych dotyczących gramatyki i grafów.         
**count**    | Number | Nie<br>wartość domyślna: 10 | Maksymalna liczba interpretacji do zwrócenia.         
**offset**   | Number | Nie<br>wartość domyślna: 0  | Indeks pierwszej interpretacji do zwrócenia. Na przykład *Liczba = 2 & przesunięcie = 0* zwraca interpretacje 0 i 1. *Liczba = 2 & przesunięcie = 2* zwraca interpretacje 2 i 3.       
**limit czasu**  | Number | Nie<br>wartość domyślna: 1000 | Limit czasu (w milisekundach). Zwracane są tylko interpretacje znalezione przed upływem limitu czasu.

<br>
  
## <a name="response-json"></a>Odpowiedź (JSON)

Name (Nazwa)     | Opis
---------|---------
**query** |Parametr *zapytania* z żądania.
**interpretacje** |Tablica wartości 0 lub więcej różnych sposobów dopasowania danych wejściowych użytkownika do gramatyki.
**interpretations[x].logprob**  |Względne prawdopodobieństwo interpretacji logarytmu naturalnego. Większe wartości są bardziej podobne.
**interpretations[x].parse**  |Ciąg XML, który pokazuje, jak jest interpretowana każda część zapytania.
**interpretacje [x]. reguły**  |Tablica z 1 lub większą liczbą reguł zdefiniowanych w gramatyce, które zostały wywołane podczas interpretacji. W przypadku Academic Knowledge API będzie zawsze istnieć 1 reguła.
**interpretacje [x]. reguły [y]. nazwa**  |Nazwa reguły.
**interpretacje [x]. reguły [y]. dane wyjściowe**  |Dane wyjściowe reguły.
**interpretacje [x]. reguły [y]. Output. Type** |Typ danych wyjściowych reguły.  W przypadku Academic Knowledge API będzie to zawsze "zapytanie".
**interpretacje [x]. reguły [y]. Output. Value**  |Dane wyjściowe reguły. W Academic Knowledge API jest to ciąg wyrażenia zapytania, który może być przesłany do metod szacowania i calchistogram.
**aborted** | Ma wartość true, jeśli upłynął limit czasu żądania.

<br>

#### <a name="example"></a>Przykład:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Poniższa odpowiedź zawiera dwa pierwsze (z powodu liczby parametrów *= 2*) najprawdopodobniej interpretacje kończące się częściowym papierem danych wejściowych użytkownika *przez Jaime*: *papiery Jaime teevan* i *dokumenty przez Jaime zielony*.  Usługa wygenerowała zapytania uzupełniające, zamiast rozważać tylko dokładne dopasowania dla autora *Jaime* , ponieważ określone żądanie *zostało ukończone = 1*. Należy zauważyć, że wartość kanoniczna *j l zielono* dopasowana za pośrednictwem synonimu *Joanna zieleń*, jak wskazano w polu analiza.


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
<br>Aby pobrać wyniki jednostki do interpretacji, użyj *wartości Output. Value* z **interpretera** API i przekaż go do interfejsu API **szacowania** za pośrednictwem parametru *Expr* . W tym przykładzie zapytanie dla pierwszej interpretacji jest następujące: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
