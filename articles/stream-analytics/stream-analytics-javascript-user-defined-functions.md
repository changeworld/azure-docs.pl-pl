---
title: 'Samouczek: Funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics | Microsoft Docs '
description: W tym samouczku przedstawiono korzystanie z zaawansowanej mechaniki zapytań za pomocą funkcji języka JavaScript zdefiniowanych przez użytkownika
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: c7414ee159303465d6698ce9c47d04ba37c0c46e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329382"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Samouczek: Funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics
 
Usługa Azure Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika, które napisano w języku JavaScript. Bogaty zestaw metod do obsługi **ciągów**, **wyrażeń regularnych**, **operacji matematycznych**, **tablic** i **danych** oferowanych przez język JavaScript ułatwia tworzenie złożonych transformacji danych w ramach zadań usługi Stream Analytics.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Określanie funkcji języka JavaScript zdefiniowanej przez użytkownika
> * Dodawanie funkcji do portalu
> * Definiowanie zapytania uruchamiającego funkcję

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="javascript-user-defined-functions"></a>Funkcje języka JavaScript zdefiniowane przez użytkownika
Funkcje języka JavaScript zdefiniowane przez użytkownika obejmują bezstanowe funkcje skalarne przeznaczone tylko do obliczeń, które nie wymagają łączności zewnętrznej. Wartość zwracana przez funkcję może być tylko wartością skalarną (pojedynczą). Po dodaniu funkcji języka JavaScript zdefiniowanej przez użytkownika do zadania możesz jej używać we wszystkich miejscach zapytania — tak jak wbudowanej funkcji skalarnej.

Poniżej przedstawiono kilka scenariuszy, w których funkcje języka JavaScript zdefiniowane przez użytkownika mogą być przydatne:
* Analizowanie ciągów zawierających funkcje do obsługi wyrażeń regularnych, na przykład **Regexp_Replace()** i **Regexp_Extract()** , oraz wykonywanie operacji na takich ciągach
* Dekodowanie i kodowanie danych, na przykład konwersja danych binarnych na szesnastkowe
* Wykonywanie obliczeń matematycznych za pomocą funkcji **matematycznych** języka JavaScript
* Wykonywanie operacji na tablicach, takich jak sortowanie, łączenie, przeszukiwanie i wypełnianie

Poniżej przedstawiono niektóre działania, których nie można wykonać w usłudze Stream Analytics przy użyciu funkcji języka JavaScript zdefiniowanej przez użytkownika:
* Wywoływanie zewnętrznych punktów końcowych usługi REST, na przykład wykonywanie odwrotnego wyszukiwania adresu IP lub pobieranie danych referencyjnych z zewnętrznego źródła
* Wykonywanie niestandardowej serializacji lub deserializacji formatu zdarzeń dla danych wejściowych lub wyjściowych
* Tworzenie niestandardowych wartości zagregowanych

Mimo że funkcje takie jak **Date.GetDate()** lub **Math.random()** nie są blokowane w definicji funkcji, należy unikać używania ich. Te funkcje **nie zwracają** tego samego wyniku za każdym wywołaniem, a usługa Azure Stream Analytics nie przechowuje dziennika wywołań funkcji i zwróconych wyników. Jeśli funkcja zwraca różne wyniki dla tych samych zdarzeń, powtarzalność nie jest gwarantowana po ponownym uruchomieniu zadania przez użytkownika lub usługę Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Dodawanie funkcji języka JavaScript zdefiniowanej przez użytkownika w witrynie Azure Portal
Aby utworzyć prostą funkcję języka JavaScript zdefiniowane przez użytkownika w ramach istniejącego zadania usługi Stream Analytics, wykonaj następujące kroki:

> [!NOTE]
> Następujące kroki działają na zadania usługi Stream Analytics skonfigurowane do uruchamiania w chmurze. Jeśli zadanie usługi Stream Analytics jest skonfigurowany do uruchamiania w usłudze Azure IoT Edge, zamiast tego użyć programu Visual Studio i [dokonany zapis przy użyciu funkcji zdefiniowanej przez użytkownika C# ](stream-analytics-edge-csharp-udf.md).

1.  W witrynie Azure Portal znajdź zadanie usługi Stream Analytics.

2. W obszarze **topologia zadań** nagłówka, wybierz **funkcji**. Zostanie wyświetlona pusta lista funkcji.

3.  Aby utworzyć nowe funkcji zdefiniowanej przez użytkownika, wybierz **+ Dodaj**.

4.  W bloku **Nowa funkcja** wybierz dla elementu **Typ funkcji** pozycję **JavaScript**. Domyślny szablon funkcji zostanie wyświetlony w edytorze.

5.  W polu **Alias funkcji zdefiniowanej przez użytkownika** podaj wartość **hex2Int** i zmień implementację funkcji w następujący sposób:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Wybierz pozycję **Zapisz**. Funkcja zostanie wyświetlona na liście funkcji.
7.  Wybierz nową funkcję **hex2Int** i sprawdź definicję funkcji. Wszystkie aliasy funkcji mają dodany prefiks **UDF**. Musisz *dołączyć prefiks* podczas wywoływania funkcji w zapytaniu usługi Stream Analytics. Należy wtedy użyć wywołania **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Wywoływanie funkcji w języku JavaScript zdefiniowanej przez użytkownika w zapytaniu

1. W edytorze zapytań w ramach **topologia zadań** nagłówka, wybierz **zapytania**.
2.  Zmodyfikuj zapytanie, a następnie wywołaj funkcję zdefiniowaną przez użytkownika w następujący sposób:

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Aby przekazać przykładowy plik danych, kliknij prawym przyciskiem myszy wejście zadania.
4.  Aby przetestować zapytanie, wybierz polecenie **Testuj**.


## <a name="supported-javascript-objects"></a>Obsługiwane obiekty języka JavaScript
Funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics obsługują standardowe wbudowane obiekty języka JavaScript. Aby uzyskać listę tych obiektów, zobacz artykuł [Global Objects (Obiekty globalne)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Konwersja typów usługi Stream Analytics i języka JavaScript

Między typami obsługiwanymi przez język zapytań usługi Stream Analytics i język JavaScript występują różnice. Poniższa tabela zawiera mapowania konwersji między typami:

Stream Analytics | JavaScript
--- | ---
bigint | Number (maksymalna liczba całkowita, która może być reprezentowana przez język JavaScript, to 2^53)
DateTime | Date (język JavaScript obsługuje tylko milisekundy)
double | Liczba
nvarchar(MAX) | String
Rekord | Object
Tablica | Tablica
NULL | Null


W tym miejscu przedstawiono konwersje typów języka JavaScript na typy usługi Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Liczba | Bigint (jeśli liczba jest zaokrąglona i należy do zakresu long.MinValue-long.MaxValue; w przeciwnym razie to double)
Date | DateTime
String | nvarchar(MAX)
Object | Rekord
Tablica | Tablica
Null, Undefined | NULL
Każdy inny typ (na przykład function lub error) | Nieobsługiwane (powoduje wystąpienie błędu w czasie wykonywania)

Język JavaScript jest uwzględniana wielkość liter i wielkość liter w wyrazie pola obiektu w kodzie JavaScript musi odpowiadać wielkości liter pól w danych przychodzących. Należy pamiętać, że zadania o poziomie zgodności 1.0 spowoduje wykonanie konwersji pola instrukcję SQL SELECT, aby zawierać tylko litery. Równy podanemu poziomowi zgodności 1.1 lub nowszej pola z instrukcją SELECT ma tą samą wielkością liter, jak określono w zapytaniu SQL.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Błędy w czasie wykonywania kodu JavaScript są traktowane jako błędy krytyczne i uwidaczniane w dzienniku aktywności. Aby pobrać dziennik, w witrynie Azure Portal przejdź do zadania i wybierz pozycję **Dziennik aktywności**.


## <a name="other-javascript-user-defined-function-patterns"></a>Inne wzorce funkcji języka JavaScript zdefiniowanej przez użytkownika

### <a name="write-nested-json-to-output"></a>Zapisywanie zagnieżdżonego kodu JSON do wyjścia
Jeśli stosujesz kolejny krok przetwarzania, który używa wyjścia zadania usługi Stream Analytics jako wejścia i wymaga formatu JSON, możesz zapisać ciąg JSON do wyjścia. W następnym przykładzie jest wywoływana funkcja **JSON.stringify()** w celu spakowania wszystkich par nazwa-wartość w danych wejściowych, a następnie zapisania ich w postaci pojedynczego ciągu do wyjścia.

**Definicja funkcji języka JavaScript zdefiniowanej przez użytkownika:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Przykładowe zapytanie:**
```SQL
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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użycie zadania w przyszłości, możesz zatrzymać je i uruchomić ponownie później w razie potrzeby. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu.  
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

W ramach tego samouczka utworzono zadanie usługi Stream Analytics, które uruchamia prostą funkcję języka JavaScript zdefiniowaną przez użytkownika. Aby dowiedzieć się więcej o usłudze Stream Analytics, zapoznaj się z artykułami dotyczącymi scenariusza w czasie rzeczywistym:

> [!div class="nextstepaction"]
> [Analiza opinii w usłudze Twitter w czasie rzeczywistym za pomocą usługi Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
