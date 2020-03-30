---
title: Agregaty zdefiniowane przez użytkownika JavaScript w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób wykonywania zaawansowanych mechaniki zapytań za pomocą agregatów zdefiniowanych przez użytkownika JavaScript w usłudze Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: c509d174787a58abeee33e039eb7bbbcbcb43f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531738"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Agregacje zdefiniowane przez użytkownika usługi Azure Stream Analytics w języku JavaScript
 
Usługa Azure Stream Analytics obsługuje agregaty zdefiniowane przez użytkownika (UDA) napisane w języku JavaScript, umożliwia implementację złożonej logiki biznesowej stanowej. W ramach UDA masz pełną kontrolę nad strukturą danych o stanie, akumulacją stanu, dekumulacją stanu i obliczeniami wyników zagregowanych. W tym artykule przedstawiono dwa różne interfejsy JavaScript UDA, kroki tworzenia UDA i jak używać UDA z operacjami opartymi na oknach w zapytaniu usługi Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregaty zdefiniowane przez użytkownika w języku JavaScript

Agregacja zdefiniowana przez użytkownika jest używana na podstawie specyfikacji przedziału czasu do agregacji zdarzeń w tym oknie i uzyskania pojedynczej wartości wyniku. Istnieją dwa typy interfejsów UDA, które stream analytics obsługuje dzisiaj, AccumulateOnly i AccumulateDeaccumulate. Oba typy UDA mogą być używane przez Tumbling, Hopping, Sliding i Session Window. AccumulateDeaccumulate UDA działa lepiej niż AccumulateOnly UDA, gdy jest używany razem z Hopping, Sliding i Session Window. Wybierz jeden z dwóch typów na podstawie algorytmu, którego używasz.

### <a name="accumulateonly-aggregates"></a>Akumulujtylko agregaty

AccumulateOnly agreguje można gromadzić tylko nowe zdarzenia do jego stanu, algorytm nie zezwala na deakumulację wartości. Wybierz ten typ agregacji, gdy deaccumulate informacji o zdarzeniu z wartości stanu jest niemożliwe do zaimplementowania. Poniżej znajduje się szablon JavaScript dla agregatów AccumulatOnly:

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

### <a name="accumulatedeaccumulate-aggregates"></a>AkumulujZaksetrykce agregatów

AccumulateDeaccumulate agregaty umożliwiają odwękamulację poprzedniej skumulowanej wartości ze stanu, na przykład usunąć parę klucz-wartość z listy wartości zdarzeń lub odjąć wartość ze stanu sumy agregacji. Poniżej znajduje się szablon JavaScript dla agregatów Akumulujobokobętość:

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

## <a name="uda---javascript-function-declaration"></a>UDA - Deklaracja funkcji JavaScript

Każdy JavaScript UDA jest zdefiniowany przez deklarację obiektu funkcji. Poniżej przedstawiono główne elementy definicji UDA.

### <a name="function-alias"></a>Alias funkcji

Alias funkcji jest identyfikatorem UDA. Po wywołaniu w zapytaniu usługi Stream Analytics zawsze używaj aliasu UDA wraz z "uda". .

### <a name="function-type"></a>Typ funkcji

W przypadku UDA typem funkcji powinien być **Javascript UDA**.

### <a name="output-type"></a>Typ wyjścia

Określony typ, który obsługiwane zadanie usługi Stream Analytics lub "Dowolny", jeśli chcesz obsłużyć typ w kwerendzie.

### <a name="function-name"></a>Nazwa funkcji

Nazwa tego obiektu Function. Nazwa funkcji powinna być zgodna z aliasem UDA.

### <a name="method---init"></a>Metoda - init()

Metoda init() inicjuje stan agregacji. Ta metoda jest wywoływana po uruchomieniu okna.

### <a name="method--accumulate"></a>Metoda – accumulate()

Metoda accumulate() oblicza stan UDA na podstawie poprzedniego stanu i bieżących wartości zdarzeń. Ta metoda jest wywoływana, gdy zdarzenie wchodzi do przedziału czasu (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW lub SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Metoda – deaccumulate()

Metoda deaccumulate() ponownie oblicza stan na podstawie poprzedniego stanu i bieżących wartości zdarzeń. Ta metoda jest wywoływana, gdy zdarzenie opuszcza SLIDINGWINDOW lub SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda – deaccumulateState()

Metoda deaccumulateState() ponownie oblicza stan na podstawie poprzedniego stanu i stanu przeskoku. Ta metoda jest wywoływana, gdy zestaw zdarzeń pozostawić HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda – computeResult()

Metoda computeResult() zwraca wynik zagregowany na podstawie bieżącego stanu. Ta metoda jest wywoływana na końcu przedziału czasu (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW lub SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA obsługiwane typy danych wejściowych i wyjściowych
W przypadku typów danych JavaScript UDA zapoznaj się z sekcją **Stream Analytics i konwersją typu JavaScript** [z integruj pliki UDF JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Dodawanie kodu JavaScript UDA z witryny Azure portal

Poniżej przechodzimy przez proces tworzenia UDA z portalu. Przykładem, którego używamy, jest obliczanie średnich ważonych czasu.

Teraz utwórzmy JavaScript UDA w ramach istniejącego zadania ASA, wykonując następujące kroki.

1. Zaloguj się do witryny Azure portal i znajdź istniejące zadanie usługi Stream Analytics.
1. Następnie kliknij na funkcje link w **obszarze JOB TOPOLOGY**.
1. Kliknij ikonę **Dodaj,** aby dodać nową funkcję.
1. W widoku Nowa funkcja wybierz **JavaScript UDA** jako typ funkcji, a następnie zobaczysz domyślny szablon UDA w edytorze.
1. Wypełnij "TWA" jako alias UDA i zmień implementację funkcji w następujący sposób:

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

1. Po kliknięciu przycisku "Zapisz" twoja UDA pojawi się na liście funkcji.

1. Kliknij na nową funkcję "TWA", możesz sprawdzić definicję funkcji.

## <a name="calling-javascript-uda-in-asa-query"></a>Wywoływanie kodu JavaScript UDA w kwerendzie ASA

W witrynie Azure portal i otwórz zadanie, edytuj kwerendę i wywołaj funkcję TWA() z prefiksem mandatu "uda.". Przykład:

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

## <a name="testing-query-with-uda"></a>Zapytanie testowe z UDA

Utwórz lokalny plik JSON z poniższą zawartością, przekaż plik do zadania usługi Stream Analytics i przetestuj powyższą kwerendę.

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

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Odwołanie do języka kwerendy usługi Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odwołanie interfejsu API REST do zarządzania usługą Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
