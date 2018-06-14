---
title: Agregacje zdefiniowane przez użytkownika JavaScript w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób wykonywania mechanika zaawansowanych zapytań z języka JavaScript agregacje zdefiniowane przez użytkownika w usłudze Azure Stream Analytics.
services: stream-analytics
author: minhe-msft
ms.author: minhe
manager: santoshb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 718109d17309747a3c19f22921e4a316b0b88dc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30907327"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics JavaScript zdefiniowane przez użytkownika agregatów (wersja zapoznawcza)

Usługa Azure Stream Analytics obsługuje zdefiniowane przez użytkownika agregatów (UDA) napisane w języku JavaScript, można wdrożyć logikę biznesową stanowe złożonych. W ramach UDA masz pełną kontrolę nad struktury danych stanu, gromadzenia stanu, stanu wyłączenia i obliczeń łączny wynik. Artykuł wprowadza dwa różne interfejsy JavaScript UDA, kroki, aby utworzyć UDA i sposobu użycia UDA z okna na podstawie operacji w zapytaniu Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregacje zdefiniowane przez użytkownika JavaScript

Agregacja zdefiniowana przez użytkownika jest używany na górze Specyfikacja okna czasu do agregacji zdarzeń w danym przedziale oraz tworzenia pojedynczej wartości wynikowej. Istnieją dwa typy interfejsów UDA, że analiza strumienia obsługuje obecnie AccumulateOnly i AccumulateDeaccumulate. Oba typy UDA mogą posłużyć okno wirowania, skaczące okna i przedłużanie okna. AccumulateDeaccumulate UDA wykonuje lepszą wydajność niż UDA AccumulateOnly, gdy jest używany z okna skaczące i przesuwanie. Możesz wybrać jeden z dwóch typów, na podstawie których można używać algorytmu.

### <a name="accumulateonly-aggregates"></a>Agreguje AccumulateOnly

Agreguje AccumulateOnly może spowodować zgromadzenie tylko nowe zdarzenia do jego stanu, algorytm nie zezwala na deaccumulation wartości. Wybierz ten typ agregacji podczas deaccumulate zdarzenia informacji z wartością stanu jest niemożliwy do wykonania. Szablon języka JavaScript dla wartości zagregowanych AccumulatOnly jest następujący:

````JavaScript
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
````

### <a name="accumulatedeaccumulate-aggregates"></a>Agreguje AccumulateDeaccumulate

Agreguje AccumulateDeaccumulate Zezwalaj deaccumulation poprzednią wartość skumulowana ze stanu, na przykład, usunąć parę klucz wartość z listy wartości zdarzenia lub odjęcie wartości ze stanu agregacji sum. Szablon języka JavaScript dla wartości zagregowanych AccumulateDeaccumulate jest następujący:

````JavaScript
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
````

## <a name="uda---javascript-function-declaration"></a>UDA - deklaracji funkcji JavaScript

Każdy UDA JavaScript jest zdefiniowana przez deklarację funkcji obiektu. Poniżej przedstawiono główne elementy w definicji UDA.

### <a name="function-alias"></a>Alias funkcji

Funkcja alias jest identyfikatorem UDA. Wywołanego w zapytaniu Stream Analytics, zawsze używaj alias UDA wraz z "uda". prefiks.

### <a name="function-type"></a>Typ funkcji

Dla UDA, powinien być typu funkcji **Javascript UDA**.

### <a name="output-type"></a>Typ wyjścia

Określony typ zadania usługi analiza strumienia obsługiwane lub "Wszystkie" Jeśli chcesz obsługiwać typ w zapytaniu.

### <a name="function-name"></a>Nazwa funkcji

Nazwa tego obiektu funkcji. Nazwa funkcji dosłownie powinna być zgodna z aliasem UDA (Podgląd zachowanie, firma Microsoft rozważa obsługę funkcji anonimowej podczas GA).

### <a name="method---init"></a>Metoda - init()

Metoda init() inicjuje stan agregacji. Ta metoda jest wywoływana po uruchomieniu okna.

### <a name="method--accumulate"></a>Metoda — accumulate()

Metoda accumulate() oblicza stan UDA, w zależności od bieżącej wartości zdarzeń i poprzedniego stanu. Ta metoda jest wywoływana, gdy zdarzenie wprowadza okno czasu (TUMBLINGWINDOW, HOPPINGWINDOW lub SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Metoda — deaccumulate()

Metoda deaccumulate() ponownie oblicza stanu na podstawie poprzedniego stanu i bieżące wartości zdarzeń. Ta metoda jest wywoływana, gdy zdarzenie opuści SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda — deaccumulateState()

Metoda deaccumulateState() ponownie oblicza stanu na podstawie poprzedniego stanu i stan przeskoku. Ta metoda jest wywoływana, gdy zestaw zdarzeń pozostawisz HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda — computeResult()

Metoda computeResult() zwraca łączny wynik na podstawie bieżącego stanu. Ta metoda jest wywoływana po zakończeniu okno czasu (TUMBLINGWINDOW HOPPINGWINDOW i SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA obsługiwane typy danych wejściowych i wyjściowych
Typy danych języka JavaScript UDA, znajdują się w sekcji **konwersja typu Stream Analytics i języka JavaScript** z [integracji funkcji UDF języka JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Dodawanie JavaScript UDA z portalu Azure

Poniżej opisano firma Microsoft za pośrednictwem procesu tworzenia UDA z portalu. Używana tutaj przykładzie Trwa obliczanie średnie ważone w czasie.

Teraz Utwórzmy UDA JavaScript, w obszarze na istniejące zadanie ASA, wykonując kroki.

1. Zaloguj się do portalu Azure i Znajdź istniejące zadania usługi analiza strumienia.
1. Następnie kliknij łącze funkcji w obszarze **TOPOLOGII zadania**.
1. Polecenie **Dodaj** ikonę, aby dodać nową funkcję.
1. W widoku nową funkcję, wybierz **JavaScript UDA** jako typ funkcji następnie zostanie wyświetlony domyślny szablon UDA widoczne w edytorze.
1. Wypełnij "TWA" jako UDA alias i zmień implementację funkcji w następujący sposób:

    ````JavaScript
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
    ````

1. Po kliknięciu przycisku "Zapisz" Twoje UDA zostaną wyświetlone na liście funkcji.

1. Kliknij na nową funkcję "TWA", możesz sprawdzić definicji funkcji.

## <a name="calling-javascript-uda-in-asa-query"></a>Wywoływanie JavaScript UDA w zapytaniu ASA

W portalu Azure i Otwórz swoją pracę, Edytuj zapytanie i wywołania funkcji TWA() z prefiksem upoważnienia "uda.". Na przykład:

````SQL
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
````

## <a name="testing-query-with-uda"></a>Testowanie zapytań z UDA

Utwórz lokalny plik JSON o poniżej zawartości, przekazać pliku do zadania usługi analiza strumienia i przetestowania powyżej zapytania.

````JSON
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
````

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Usługa Azure Stream Analytics management dokumentacji interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
