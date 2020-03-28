---
title: Funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics
description: Ten artykuł jest wprowadzeniem do funkcji zdefiniowanych przez użytkownika JavaScript w usłudze Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235389"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Funkcje zdefiniowane przez użytkownika JavaScript w usłudze Azure Stream Analytics
 
Usługa Azure Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika, które napisano w języku JavaScript. Dzięki bogatemu zestawowi metod **String**, **RegExp**, **Math**, **Array**i **Date,** które zapewnia JavaScript, tworzenie złożonych przekształceń danych za pomocą zadań usługi Stream Analytics staje się łatwiejsze do utworzenia.

## <a name="overview"></a>Omówienie

Funkcje zdefiniowane przez użytkownika JavaScript obsługują funkcje skalarne tylko dla jednostek obliczeniowych, które nie wymagają łączności zewnętrznej. Wartość zwracana przez funkcję może być tylko wartością skalarną (pojedynczą). Po dodaniu funkcji języka JavaScript zdefiniowanej przez użytkownika do zadania możesz jej używać we wszystkich miejscach zapytania — tak jak wbudowanej funkcji skalarnej.

Poniżej przedstawiono kilka scenariuszy, w których funkcje języka JavaScript zdefiniowane przez użytkownika mogą być przydatne:
* Analizowanie ciągów zawierających funkcje do obsługi wyrażeń regularnych, na przykład **Regexp_Replace()** i **Regexp_Extract()**, oraz wykonywanie operacji na takich ciągach
* Dekodowanie i kodowanie danych, na przykład konwersja danych binarnych na szesnastkowe
* Wykonywanie obliczeń matematycznych za pomocą funkcji **javascript matematyki**
* Wykonywanie operacji tablicowych, takich jak sortowanie, dołączanie, znajdowanie i wypełnianie

Oto kilka czynności, których nie można wykonać za pomocą funkcji zdefiniowanej przez użytkownika JavaScript w usłudze Stream Analytics:
* Wywoływanie zewnętrznych punktów końcowych REST, na przykład wykonywanie wyszukiwania odwrotnego adresu IP lub ściąganie danych referencyjnych ze źródła zewnętrznego
* Wykonywanie niestandardowej serializacji lub deserializacji formatu zdarzeń dla danych wejściowych lub wyjściowych
* Tworzenie niestandardowych wartości zagregowanych

Chociaż funkcje takie jak **Date.GetDate()** lub **Math.random()** nie są blokowane w definicji funkcji, należy unikać ich używania. Te funkcje **nie** zwracają ten sam wynik za każdym razem, gdy do nich dzwonisz, a usługa Azure Stream Analytics nie przechowuje dziennika wywołań funkcji i zwracanych wyników. Jeśli funkcja zwraca inny wynik w tych samych zdarzeniach, powtarzalność nie jest gwarantowana po ponownym uruchomieniu zadania przez Ciebie lub przez usługę Usługi Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Dodawanie funkcji zdefiniowanej przez użytkownika w języku JavaScript do zadania

> [!NOTE]
> Te kroki działają na zadaniach usługi Stream Analytics skonfigurowanych do uruchamiania w chmurze. Jeśli zadanie usługi Stream Analytics jest skonfigurowane do uruchamiania w usłudze Azure IoT Edge, zamiast tego użyj programu Visual Studio i [napisz funkcję zdefiniowaną przez użytkownika przy użyciu języka C#](stream-analytics-edge-csharp-udf.md).

Aby utworzyć funkcję zdefiniowaną przez użytkownika w języku JavaScript w zadaniu usługi Stream Analytics, wybierz pozycję **Funkcje** w obszarze **Topologia zadań**. Następnie wybierz **javascript UDF** z menu rozwijanego **+Dodaj.** 

![Dodawanie udf języka JavaScript](./media/javascript/stream-analytics-jsudf-add.png)

Następnie należy podać następujące właściwości i wybrać **zapisz**.

|Właściwość|Opis|
|--------|-----------|
|Alias funkcji|Wprowadź nazwę, aby wywołać funkcję w kwerendzie.|
|Typ wyjścia|Wpisz, które zostaną zwrócone przez funkcję zdefiniowaną przez użytkownika JavaScript do zapytania usługi Stream Analytics.|
|Definicja funkcji|Implementacja funkcji JavaScript, która będzie wykonywana za każdym razem, gdy UDF zostanie wywołana z zapytania.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testowanie i rozwiązywanie problemów z plikówami JAVAScript 

Logikę interfejsu UDF języka JavaScript można przetestować i debugować w dowolnej przeglądarce. Debugowanie i testowanie logiki tych funkcji zdefiniowanych przez użytkownika nie jest obecnie obsługiwane w portalu usługi Stream Analytics. Gdy funkcja działa zgodnie z oczekiwaniami, można dodać go do zadania usługi Stream Analytics, jak wspomniano powyżej, a następnie wywołać go bezpośrednio z zapytania. Logikę kwerend można przetestować za pomocą języka JavaScript UDF przy użyciu [narzędzi usługi Stream Analytics dla programu Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

Błędy w czasie wykonywania kodu JavaScript są traktowane jako błędy krytyczne i uwidaczniane w dzienniku aktywności. Aby pobrać dziennik, w witrynie Azure Portal przejdź do zadania i wybierz pozycję **Dziennik aktywności**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Wywoływanie funkcji w języku JavaScript zdefiniowanej przez użytkownika w zapytaniu

Funkcję JavaScript można łatwo wywołać w zapytaniu przy użyciu aliasu funkcji poprzedzonym **udf**. Oto przykład UDF JavaScript, który konwertuje wartości szesnastkowe na liczbę całkowitą wywoływaną w kwerendzie usługi Stream Analytics.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Obsługiwane obiekty języka JavaScript

Funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics obsługują standardowe wbudowane obiekty języka JavaScript. Aby uzyskać listę tych obiektów, zobacz artykuł [Global Objects (Obiekty globalne)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Konwersja typów usługi Stream Analytics i języka JavaScript

Między typami obsługiwanymi przez język zapytań usługi Stream Analytics i język JavaScript występują różnice. Poniższa tabela zawiera mapowania konwersji między typami:

Stream Analytics | JavaScript
--- | ---
bigint | Number (maksymalna liczba całkowita, która może być reprezentowana przez język JavaScript, to 2^53)
DateTime | Date (język JavaScript obsługuje tylko milisekundy)
double | Liczba
nvarchar(MAX) | Ciąg
Rekord | Obiekt
Tablica | Tablica
NULL | Null

W tym miejscu przedstawiono konwersje typów języka JavaScript na typy usługi Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Liczba | Bigint (jeśli liczba jest zaokrąglona i należy do zakresu long.MinValue-long.MaxValue; w przeciwnym razie to double)
Data | DateTime
Ciąg | nvarchar(MAX)
Obiekt | Rekord
Tablica | Tablica
Null, Undefined | NULL
Każdy inny typ (na przykład function lub error) | Nieobsługiwane (powoduje wystąpienie błędu w czasie wykonywania)

W języku JavaScript rozróżniana jest wielkość liter, a wielkość liter w polach obiektów w kodzie JavaScript musi być zgodna z wielkością liter pól w danych przychodzących. Zadania z poziomem zgodności 1.0 konwertują pola z instrukcji SQL SELECT jako małe litery. W obszarze poziomu zgodności 1.1 lub nowszego pola z instrukcji SELECT będą miały taką samą wielkość liter, jak określono w kwerendzie SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Inne wzorce funkcji języka JavaScript zdefiniowanej przez użytkownika

### <a name="write-nested-json-to-output"></a>Zapisywanie zagnieżdżonego kodu JSON do wyjścia

Jeśli stosujesz kolejny krok przetwarzania, który używa wyjścia zadania usługi Stream Analytics jako wejścia i wymaga formatu JSON, możesz zapisać ciąg JSON do wyjścia. W następnym przykładzie jest wywoływana funkcja **JSON.stringify()** w celu spakowania wszystkich par nazwa-wartość w danych wejściowych, a następnie zapisania ich w postaci pojedynczego ciągu do wyjścia.

**Definicja funkcji języka JavaScript zdefiniowanej przez użytkownika:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Przykładowa kwerenda:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>Następne kroki

* [Uczenie maszynowe UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
