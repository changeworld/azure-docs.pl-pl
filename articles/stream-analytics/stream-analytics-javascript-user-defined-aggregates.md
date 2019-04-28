---
title: Agregacje JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób zaawansowanej mechaniki zapytań za pomocą języka JavaScript agregacje zdefiniowane przez użytkownika w usłudze Azure Stream Analytics.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 6663e3fc48408de83e92f39e8c8070005818852d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479562"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Usługa Azure Stream Analytics JavaScript agregacje zdefiniowane przez użytkownika (wersja zapoznawcza)
 
Usługa Azure Stream Analytics obsługuje agregacje zdefiniowane przez użytkownika (UDA) napisanych w języku JavaScript, pozwala na implementowanie złożoną stanowych logikę biznesową. W ramach UDA masz pełną kontrolę nad struktury danych stanu, stanu gromadzenia, stan wyłączenia i agregacji wyników obliczeń. Artykuł wprowadza dwa różne interfejsy Agregacja uda w JĘZYKU JavaScript, kroki umożliwiające utworzenie agregacji i jak agregacja uda w JĘZYKU za pomocą operacji opartych na okno w zapytaniu usługi Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregacje zdefiniowane przez użytkownika języka JavaScript

Agregacja zdefiniowana przez użytkownika jest używana na podstawie specyfikacji okno czasu do agregowania zdarzeń w tym oknie i tworzenia pojedynczej wartości wynikowej. Istnieją dwa typy interfejsów UDA, że usługi Stream Analytics obsługuje obecnie AccumulateOnly i AccumulateDeaccumulate. Oba rodzaje UDA może służyć przez okno wirowania, przeskokiem okna i oknie kroczącym. Agregacja uda w JĘZYKU AccumulateDeaccumulate działa lepiej niż AccumulateOnly UDA, gdy jest używana wraz z oknem przeskokiem i oknem kroczącym. Możesz wybrać jeden z dwóch typów, na podstawie algorytmu, którego używasz.

### <a name="accumulateonly-aggregates"></a>Agregacje AccumulateOnly

Agregacje AccumulateOnly tylko może wzrosnąć nowe zdarzenia do stanu, algorytm nie zezwala na deaccumulation wartości. Wybierz ten typ agregacji po deaccumulate zdarzenie informacje z wartość stanu jest niemożliwe do zaimplementowania. Poniżej przedstawiono szablon języka JavaScript dla wartości zagregowanych AccumulatOnly:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>Agregacje AccumulateDeaccumulate

Agregacje AccumulateDeaccumulate zezwalanie na deaccumulation poprzednią wartość skumulowana ze stanu, na przykład, Usuń pary klucz wartość z listy wartości zdarzeń lub Odejmij wartość z zakresu od stanu agregacji sum. Poniżej przedstawiono szablon języka JavaScript dla wartości zagregowanych AccumulateDeaccumulate:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>Agregacja uda w JĘZYKU — JavaScript deklaracji funkcji

Każdy UDA języka JavaScript jest definiowany przez deklarację obiektu funkcji. Poniżej przedstawiono główne elementy w definicji UDA.

### <a name="function-alias"></a>Alias funkcji

Alias funkcji jest identyfikatorem UDA. Gdy zostanie wywołana w zapytaniu usługi Stream Analytics, zawsze używaj alias UDA wraz z "agregacji". prefiks.

### <a name="function-type"></a>Typ funkcji

Dla Agregacja uda w JĘZYKU powinien być typu funkcji **Agregacja uda w JĘZYKU Javascript**.

### <a name="output-type"></a>Typ wyjścia

Określony typ zadania usługi Stream Analytics obsługiwane lub "Dowolna", jeśli chcesz obsługiwać typ w zapytaniu.

### <a name="function-name"></a>Nazwa funkcji

Nazwa tego obiektu funkcji. Nazwa funkcji dosłownie powinna odpowiadać alias Agregacja uda w JĘZYKU (zachowania, w wersji zapoznawczej firma Microsoft rozważa funkcja anonimowa pomocy technicznej podczas GA).

### <a name="method---init"></a>Metoda - init()

Metoda init() inicjuje stan agregacji. Ta metoda jest wywoływana, gdy rozpoczyna się okna.

### <a name="method--accumulate"></a>Metoda — accumulate()

Metoda accumulate() oblicza stanu UDA, na podstawie poprzedniego stanu i bieżące wartości zdarzeń. Ta metoda jest wywoływana, gdy zdarzenie wchodzi przedział czasu (TUMBLINGWINDOW, HOPPINGWINDOW lub SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Metoda — deaccumulate()

Metoda deaccumulate() ponownie oblicza stanu na podstawie poprzedniego stanu i bieżące wartości zdarzeń. Ta metoda jest wywoływana, gdy zdarzenie opuszcza SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda — deaccumulateState()

Metoda deaccumulateState() ponownie oblicza stanu na podstawie poprzedni stan i stan przeskoku. Ta metoda jest wywoływana, gdy zestaw zdarzeń pozostawisz HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda — computeResult()

Metoda computeResult() zwraca agregacji wyników na podstawie bieżącego stanu. Ta metoda jest wywoływana na końcu przedział czasu (TUMBLINGWINDOW HOPPINGWINDOW i SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Agregacja uda w JĘZYKU JavaScript obsługiwane typy danych wejściowych i wyjściowych
Typy danych Agregacja uda w JĘZYKU JavaScript, można znaleźć w sekcji **Konwersja typów usługi Stream Analytics i języka JavaScript** z [Integrowanie funkcji UDF języka JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Dodawanie UDA języka JavaScript w witrynie Azure portal

Poniżej części omówimy proces tworzenia agregacji z portalu. Przykład, używanych przez firmę Microsoft w tym miejscu jest przetwarzanie średnie ważone w czasie.

Teraz Utwórzmy UDA języka JavaScript, w ramach istniejącego zadania ASA, wykonując kroki.

1. Zaloguj się do witryny Azure portal i Znajdź istniejące zadanie usługi Stream Analytics.
1. Następnie kliknij link funkcji w obszarze **TOPOLOGIA zadań**.
1. Kliknij pozycję **Dodaj** ikonę, aby dodać nową funkcję.
1. W widoku nową funkcję, wybierz **Agregacja uda w JĘZYKU JavaScript** jako typ funkcji, zostanie wyświetlona domyślny szablon UDA, pojawiają się w edytorze.
1. Wprowadź "TWA" jako UDA alias i zmień implementację funkcji w następujący:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Po kliknięciu przycisku "Zapisz", Twoja UDA pojawia się na liście funkcji.

1. Kliknij na nową funkcję "TWA" możesz sprawdzić definicji funkcji.

## <a name="calling-javascript-uda-in-asa-query"></a>Wywoływanie Agregacja uda w JĘZYKU JavaScript w zapytaniu ASA

W witrynie Azure portal Otwórz zadanie usługi, Edytuj zapytanie i Wywołaj funkcję TWA() prefiksem upoważnienie "uda.". Na przykład:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Testowanie zapytań za pomocą UDA

Utwórz plik lokalny JSON za pomocą poniżej zawartości, przekazać plik do zadania usługi Stream Analytics i testowanie powyższe zapytanie.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dodatkową pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań w usłudze Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Usługa Azure Stream Analytics management dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
