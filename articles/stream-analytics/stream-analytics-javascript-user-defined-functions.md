---
title: Azure Stream Analytics JavaScript funkcje zdefiniowane przez użytkownika | Dokumentacja firmy Microsoft
description: Wykonaj mechanika zaawansowanych zapytań z języka JavaScript funkcje zdefiniowane przez użytkownika
keywords: JavaScript, funkcje udf zdefiniowane przez użytkownika
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: f2b14029ebea7f9cf1fa74a384ecbb72b08b7ad6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure Stream Analytics JavaScript funkcje zdefiniowane przez użytkownika
Usługa Azure Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika napisane w języku JavaScript. Z zaawansowanej zestaw **ciąg**, **RegExp**, **matematyczne**, **tablicy**, i **data** metod tego JavaScript zawiera, danych złożonych przekształceń analiza strumienia zadania stają się łatwiejsze do utworzenia.

## <a name="javascript-user-defined-functions"></a>JavaScript — funkcje zdefiniowane przez użytkownika
Funkcje zdefiniowane przez użytkownika JavaScript obsługuje bezstanowych, tylko do obliczeń funkcje skalarne, które nie wymagają łączność zewnętrzną. Wartość zwracana funkcji można tylko wartość skalarną (jeden). Po dodaniu funkcji zdefiniowanej przez użytkownika JavaScript do zadania, funkcja dowolne miejsce w zapytaniu, takich jak wbudowanych funkcji skalarnej.

Poniżej przedstawiono kilka scenariuszy, w którym może być przydatne funkcje zdefiniowane przez użytkownika JavaScript:
* Analizowanie i operowanie nimi ciągów, które mają funkcji wyrażenie regularne, na przykład **Regexp_Replace()** i **Regexp_Extract()**
* Dekodowanie i kodowania danych, na przykład konwersja binarny szesnastkowy
* Wykonywanie obliczeń mathematic JavaScript **matematyczne** funkcji
* Wykonywanie operacji na tablicy jak sortowania, sprzężenia, Znajdź i wypełnienia

Poniżej przedstawiono niektóre czynności, które nie można wykonać przy użyciu funkcji zdefiniowanej przez użytkownika JavaScript w Stream Analytics:
* Wywołanie limit zewnętrzne punkty końcowe REST, na przykład wykonywania wstecznego wyszukiwania IP lub ściągania danych referencyjnych ze źródła zewnętrznego
* Przeprowadź niestandardowe zdarzenie format serializacji lub deserializacji w danych wejściowych/wyjściowych
* Tworzenie wartości zagregowanych niestandardowych

Mimo że funkcje takie jak **Date.GetDate()** lub **Math.random()** nie są blokowane w definicji funkcji, należy unikać używania ich. Te funkcje **nie** zwracać ten sam rezultat za każdym razem należy wywołać i usługą Azure Stream Analytics nie przechowuje dziennika wywołania funkcji i zwróciło wyników. Jeśli funkcja zwraca różne wyniki na same zdarzenia, powtarzalność nie jest gwarantowana po ponownym uruchomieniu zadania przez Ciebie lub usługa Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Dodaj funkcję JavaScript zdefiniowane przez użytkownika w portalu Azure
Aby utworzyć prosty funkcji zdefiniowanej przez użytkownika JavaScript w obszarze na istniejące zadanie usługi Stream Analytics, wykonaj następujące kroki:

1.  W portalu Azure Znajdź zadania usługi analiza strumienia.
2.  W obszarze **TOPOLOGII zadania**, wybierz funkcji. Zostanie wyświetlona pusta lista funkcji.
3.  Aby utworzyć nową funkcję zdefiniowane przez użytkownika, wybierz **Dodaj**.
4.  Na **nową funkcję** bloku dla **typu funkcji**, wybierz pozycję **JavaScript**. Domyślny szablon funkcji zostanie wyświetlony w edytorze.
5.  Dla **UDF alias**, wprowadź **hex2Int**i zmień implementację funkcji w następujący sposób:

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Wybierz pozycję **Zapisz**. Funkcja zostanie wyświetlony na liście funkcji.
7.  Wybierz nową **hex2Int** funkcji i sprawdź definicję funkcji. Wszystkie funkcje mają **UDF** Prefiks dodawany do aliasu funkcji. Musisz *zawierać prefiks* gdy wywołanie funkcji w zapytaniu Stream Analytics. W takim przypadku należy wywołać **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Wywoływanie funkcji zdefiniowanej przez użytkownika JavaScript w kwerendzie

1. W edytorze zapytań w obszarze **TOPOLOGII zadania**, wybierz pozycję **zapytania**.
2.  Edytuj zapytanie, a następnie wywołania funkcji zdefiniowanej przez użytkownika, jak to:

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Aby przekazać przykładowy plik danych, kliknij prawym przyciskiem myszy dane wejściowe zadania.
4.  Aby przetestować zapytanie, wybierz **testu**.


## <a name="supported-javascript-objects"></a>Obsługiwane obiektów JavaScript
Funkcje zdefiniowane przez użytkownika JavaScript analiza strumienia Azure obsługuje standardowego, wbudowanego obiektów JavaScript. Aby uzyskać listę tych obiektów, zobacz [obiektów globalnych](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Konwersja typu Stream Analytics i języka JavaScript

Ma różnic w typach, że analiza strumienia zapytania języka i obsługi języka JavaScript. Poniższa tabela zawiera mapowania konwersji między nimi:

Stream Analytics | JavaScript
--- | ---
bigint | Numer (JavaScript może reprezentować tylko liczby całkowite maksymalnie dokładnie 2 ^ 53)
DateTime | Data (JavaScript tylko obsługuje w milisekundach)
O podwójnej precyzji | Liczba
nvarchar(max) | Ciąg
Rejestruj | Obiekt
Tablica | Tablica
NULL | Null


Poniżej przedstawiono konwersje JavaScript do Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Liczba | Bigint (Jeśli liczba jest okrągłych i między long. Wartość MinValue i długi. MaxValue; w przeciwnym razie jest podwójny)
Date | DateTime
Ciąg | nvarchar(max)
Obiekt | Rejestruj
Tablica | Tablica
Wartość null, niezdefiniowane | NULL
Innego typu (na przykład funkcja lub błąd) | Nieobsługiwane (powoduje błąd czasu wykonywania)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Błędy środowiska wykonawczego języka JavaScript są traktowane jako błąd krytyczny i są udostępniane za pośrednictwem dziennik aktywności. Aby uzyskać dostęp do dziennika, w portalu Azure, przejdź do zadania i wybierz **dziennik aktywności**.


## <a name="other-javascript-user-defined-function-patterns"></a>Innymi wzorami zdefiniowane przez użytkownika funkcja JavaScript

### <a name="write-nested-json-to-output"></a>Zapis zagnieżdżonych JSON do danych wyjściowych
Jeśli masz krok przetwarzania monitowania, który używa jako dane wejściowe zadania usługi analiza strumienia wyjściowego i wymaga formatu JSON, może zapisać ciąg JSON do danych wyjściowych. Następnym przykładzie wywołuje **JSON.stringify()** funkcja pakietu wszystkie pary nazwa/wartość w danych wejściowych, a następnie zapisać je jako pojedynczy ciąg w danych wyjściowych.

**Definicja funkcji zdefiniowanej przez użytkownika JavaScript:**

```
function main(x) {
return JSON.stringify(x);
}
```

**Przykładowe zapytanie:**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Usługa Azure Stream Analytics management dokumentacji interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
