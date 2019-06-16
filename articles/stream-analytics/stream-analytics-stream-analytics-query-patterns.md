---
title: Typowe wzorce zapytań w usłudze Azure Stream Analytics
description: W tym artykule opisano kilka typowych wzorców zapytań i projekty, które są przydatne w zadaniach usługi Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: f6971038be7404850d958de67eb4755ae7d21a29
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761967"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Przykłady dla typowych wzorców użycia usługi Stream Analytics zapytania

Zapytania w usłudze Azure Stream Analytics są wyrażone w język zapytań przypominający SQL. Konstrukcji języka są udokumentowane w artykule [dokumentacja języka zapytań usługi Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference) przewodnik. 

Projekt kwerendy można wyrazić proste logiki przekazywanego do przenoszenia danych zdarzenia ze strumienia wejściowego z jednego do danych wyjściowych magazynu danych lub jego wykorzystania sformatowanego wzorzec dopasowania i danych czasowych analizy do obliczania wartości zagregowanych za pośrednictwem różnych okien czasu, podobnie jak w [kompilacji IoT rozwiązanie przy użyciu usługi Stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) przewodnik. Możesz dołączyć dane z wielu danych wejściowych, aby połączyć przesyłanie strumieniowe zdarzeń i możecie wyszukiwań względem statyczne dane referencyjne wzbogacić wartości zdarzenia. Możesz również zapisać dane do wielu danych wyjściowych.

W tym artykule opisano rozwiązania kilka typowych wzorców zapytań, w oparciu o scenariuszy w rzeczywistych warunkach.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Praca ze złożonymi typami danych w formatach JSON i AVRO

Usługa Azure Stream Analytics obsługuje przetwarzanie zdarzeń w pliku CSV, JSON i Avro formaty danych.

JSON i Avro mogą zawierać złożonych typów, takich jak zagnieżdżone obiekty (rekordy) lub tablicami. Aby uzyskać więcej informacji na temat pracy z tych złożonych typów danych, dotyczą [danych podczas analizowania kodu JSON i AVRO](stream-analytics-parsing-json.md) artykułu.

## <a name="query-example-convert-data-types"></a>Przykład zapytania: Konwertowanie typów danych

**Opis**: Zdefiniuj typy właściwości dotyczących strumienia wejściowego. Na przykład wagi samochodu pochodzi na strumień wejściowy jako ciągi znaków i musi zostać skonwertowany do **INT** przeprowadzić **suma**.

**Dane wejściowe**:

| Tworzenie | Time | Waga |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Dane wyjściowe**:

| Tworzenie | Waga |
| --- | --- |
| Honda |3000 |

**Rozwiązanie**:

```SQL
    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Explanation**: Użyj **RZUTOWANIA** instrukcji w **wagi** pola, aby określić typ jej danych. Zobacz listę obsługiwanych typów danych w [typy danych (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Przykład zapytania: Użyj LIKE/NOT takich jak na dopasowanie wzorca

**Opis**: Sprawdź, czy wartość pola w zdarzeniu ze wzorcem niektórych.
Na przykład Sprawdź, czy wynik zwraca talerzy licencji A zaczynać się i kończyć ciągiem 9.

**Dane wejściowe**:

| Tworzenie | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Dane wyjściowe**:

| Tworzenie | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Rozwiązanie**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Explanation**: Użyj **takich jak** instrukcję, aby sprawdzić **LicensePlate** polu wartość. Powinna rozpoczynać się literą, A, a następnie mają dowolny ciąg zawierający zero lub więcej znaków i następnie kończyć się znakiem liczby 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Przykład zapytania: Określ logiki dla różnych przypadków/wartości (instrukcji CASE)

**Opis**: Podaj inną obliczeń dla pola, na podstawie określonego kryterium. Na przykład można podać, że przekazany ciąg opisu ile samochodów tego samego upewnij z szczególny przypadek 1.

**Dane wejściowe**:

| Tworzenie | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Dane wyjściowe**:

| CarsPassed | Time |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Rozwiązanie**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Explanation**: **Przypadek** wyrażenie porównuje wyrażenie zbiór proste wyrażenia do obliczenia wyniku. W tym przykładzie pojazdu sprawia, że wraz z liczbą 1 zwrócił opis ciągu innego niż pojazdu sprawia, że wraz z liczbą inna niż 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Przykład zapytania: Wysyłanie danych do wielu danych wyjściowych

**Opis**: Wysłać dane do wielu celów dane wyjściowe z jednego zadania. Na przykład analizować dane oparte na wartościach progowych alertu i archiwizowanie wszystkich zdarzeń do usługi blob storage.

**Dane wejściowe**:

| Tworzenie | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Tworzenie | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Tworzenie | Time | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Rozwiązanie**:

```SQL
    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp() AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3
```

**Explanation**: **INTO** klauzuli informuje usługi Stream Analytics której dane wyjściowe do zapisywania danych do tej instrukcji. Pierwszego zapytania jest przekazywanie danych otrzymanych do pliku wyjściowego o nazwie **ArchiveOutput**. Drugie zapytanie jest kilka prostych agregacji i filtrowanie i przesyła wyniki do podrzędnego systemu zgłaszania alertów **AlertOutput**.

Należy pamiętać, że także ponownie użyć wyników wspólnych wyrażeń tabel (wyrażeń CTE) (takich jak **WITH** instrukcji) w wielu instrukcji w danych wyjściowych. Ta opcja ma jednocześnie ma dodatkową zaletę otwierania mniejszej liczby czytelników do źródła danych wejściowych.

Na przykład: 

```SQL
    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'
```

## <a name="query-example-count-unique-values"></a>Przykład zapytania: Liczba unikatowych wartości

**Opis**: Liczbę unikatowych wartości pól, które pojawiają się w strumieniu w przedziale czasu. Na przykład jak wiele unikatowych sprawia, że samochodów przekazywane stoisku płatny w oknie 2-sekundowych?

**Dane wejściowe**:

| Tworzenie | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Dane wyjściowe:**

| CountMake | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Rozwiązanie:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Objaśnienie:** 
**COUNT (DISTINCT upewnij)** zwraca liczbę unikatowych wartości w **wprowadzić** kolumny w przedziale czasu.

## <a name="query-example-determine-if-a-value-has-changed"></a>Przykład zapytania: Określić, jeśli wartość została zmieniona

**Opis**: Spójrz na poprzedniej wartości, aby ustalić, czy jest inny niż bieżąca wartość. Na przykład jest poprzedniego samochodu na drodze płatny wykonującego ten sam jako bieżący samochodu?

**Dane wejściowe**:

| Tworzenie | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Dane wyjściowe**:

| Tworzenie | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Rozwiązanie**:

```SQL
    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**Explanation**: Użyj **LAG** wgląd do strumienia wejściowego jednego zdarzenia Wstecz i uzyskanie **wprowadzić** wartość. Następnie porównaj ją do **wprowadzić** wartość bieżącego zdarzenia i dane wyjściowe zdarzenia są różne.

## <a name="query-example-find-the-first-event-in-a-window"></a>Przykład zapytania: Znajdź pierwsze zdarzenie w oknie

**Opis**: Znajdź pierwszy samochodu co 10-minutowych interwałach.

**Dane wejściowe**:

| LicensePlate | Tworzenie | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Dane wyjściowe**:

| LicensePlate | Tworzenie | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Rozwiązanie**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1
```

Teraz możemy zmienić problemu i Znajdź pierwszego samochodu określonego upewnij w co 10-minutowych interwałach.

| LicensePlate | Tworzenie | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Rozwiązanie**:

```SQL
    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

## <a name="query-example-find-the-last-event-in-a-window"></a>Przykład zapytania: Znajdź ostatnie zdarzenie w oknie

**Opis**: Znajdź ostatnie samochodu co 10-minutowych interwałach.

**Dane wejściowe**:

| LicensePlate | Tworzenie | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Dane wyjściowe**:

| LicensePlate | Tworzenie | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Rozwiązanie**:

```SQL
    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime
```

**Explanation**: Istnieją dwa kroki w zapytaniu. Pierwszy z nich umożliwia znalezienie najnowszych sygnatura czasowa w systemie windows 10 minut. Drugi etap łączy wyniki pierwszego zapytania przy użyciu oryginalnego strumienia, aby znaleźć zdarzenia, które odpowiadają ostatniego sygnatury czasowe każdego okna. 

## <a name="query-example-detect-the-absence-of-events"></a>Przykład zapytania: Wykrywanie braku zdarzeń

**Opis**: Sprawdź, czy strumień nie ma żadnej wartości, która spełnia określone kryterium.
Na przykład 2 samochodów następujących po sobie z tym samym upewnij wprowadzono drogowej płatny w ciągu ostatnich 90 sekund?

**Dane wejściowe**:

| Tworzenie | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Dane wyjściowe**:

| Tworzenie | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Rozwiązanie**:

```SQL
    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Explanation**: Użyj **LAG** wgląd do strumienia wejściowego jednego zdarzenia Wstecz i uzyskanie **wprowadzić** wartość. Aby porównać **upewnij** wartość w bieżącym zdarzeniu, a następnie dane wyjściowe zdarzenia, jeśli są takie same. Można również użyć **LAG** można pobrać danych dotyczących samochodów poprzedniego.

## <a name="query-example-detect-the-duration-between-events"></a>Przykład zapytania: Wykrywanie czas trwania między zdarzeniami

**Opis**: Znajdź czas trwania podanego zdarzenia. Na przykład biorąc pod uwagę kliknięć w sieci web, określ czas spędzony na danej funkcji.

**Dane wejściowe**:  

| Użytkownik | Cecha | Wydarzenie | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Uruchamianie |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Dane wyjściowe**:  

| Użytkownik | Cecha | Czas trwania |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Rozwiązanie**:

```SQL
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Explanation**: Użyj **ostatniego** funkcję, aby pobrać ostatniego **czasu** wartości w przypadku typu zdarzenia **Start**. **Ostatniego** używa funkcji **PARTITION BY [użytkownik]** do wskazania, że wynik jest obliczana na unikatowych użytkowników. Zapytanie ma 1-godzinnego próg Maksymalny odstęp czasu między **Start** i **zatrzymać** zdarzeń, ale można skonfigurować zgodnie z potrzebami **(LIMIT DURATION(hour, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Przykład zapytania: Wykrywanie warunku czas trwania
**Opis**: Sprawdzanie, ile warunku wystąpił.
Na przykład załóżmy, że usterka spowodowała wszystkich samochodów niepoprawne ciężar (ponad 20 000 funtach) i musi zostać obliczony czas trwania tego błędu.

**Dane wejściowe**:

| Tworzenie | Time | Waga |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Dane wyjściowe**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Rozwiązanie**:

```SQL
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
```

**Explanation**: Użyj **LAG** Aby wyświetlić strumień wejściowy przez 24 godziny i poszukaj wystąpień gdzie **StartFault** i **StopFault** są objęte wagi < 20000.

## <a name="query-example-fill-missing-values"></a>Przykład zapytania: Wypełnienie brakujących wartości

**Opis**: Dla strumienia zdarzeń, które nie mają wartości należy utworzyć strumień zdarzeń z regularnych odstępach czasu. Na przykład generują zdarzenie co 5 sekund, któremu podlega najbardziej niedawno widziany punktu danych.

**Dane wejściowe**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Dane wyjściowe (10 pierwszych wierszy)** :

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Rozwiązanie**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Explanation**: To zapytanie generuje zdarzenia co 5 sekund, a także generuje ostatniego zdarzenia, która została otrzymana wcześniej. [Okna Hopping](/stream-analytics-query/hopping-window-azure-stream-analytics) czas trwania określa, jak daleko wstecz wygląda zapytanie można znaleźć najnowsze zdarzenie (300 sekund w tym przykładzie).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Przykład zapytania: Korelowanie dwa typy zdarzeń w ramach tego samego strumienia

**Opis**: Czasami alertów muszą być generowane na podstawie wielu typów zdarzeń, które wystąpiły w zakresie czasu. Na przykład w scenariuszu IoT dla głównego piekarników alert musi zostać wygenerowany podczas temperatura wentylator jest mniejsza niż 40 i maksymalna moc w ciągu ostatnich 3 minut jest mniejsza niż 10.

**Dane wejściowe**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | "temp" |120 |
| "2018-01-01T16:01:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:02:00" | "Oven1" | "temp" |100 |
| "2018-01-01T16:02:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:03:00" | "Oven1" | "temp" |70 |
| "2018-01-01T16:03:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:04:00" | "Oven1" | "temp" |50 |
| "2018-01-01T16:04:00" | "Oven1" | "power" |15 |
| "2018-01-01T16:05:00" | "Oven1" | "temp" |30 |
| "2018-01-01T16:05:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:06:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:06:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:07:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:07:00" | "Oven1" | "power" |8 |
| "2018-01-01T16:08:00" | "Oven1" | "temp" |20 |
| "2018-01-01T16:08:00" | "Oven1" | "power" |8 |

**Dane wyjściowe**:

| eventTime | deviceId | Temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Short circuit ogrzewania elementów" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Short circuit ogrzewania elementów" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Short circuit ogrzewania elementów" |15 |

**Rozwiązanie**:

```SQL
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp() AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
```

**Explanation**: Pierwsze zapytanie `max_power_during_last_3_mins`, używa [okna ruchomej](/stream-analytics-query/sliding-window-azure-stream-analytics) można znaleźć maksymalną wartość czujnik zasilania dla każdego urządzenia w ciągu ostatnich 3 minut. Drugie zapytanie jest dołączony do pierwszego zapytania do odnalezienia wartości zasilania w oknie najnowszych istotne dla bieżącego zdarzenia. A następnie, pod warunkiem warunki są spełnione, alert jest generowany dla tego urządzenia.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Przykład zapytania: Przetwarzanie zdarzeń niezależnie od urządzenia zegara pochylanie (substreams)

**Opis**: Zdarzenia mogą pojawić się opóźnienia lub poza kolejnością z powodu wynikających z przesunięcia czasowego zegara między producentami zdarzeń, pochyla zegara między partycjami lub opóźnienia sieci. W poniższym przykładzie zegara urządzenia TollID 2 jest pięć sekund za TollID 1 i zegara urządzenia dla TollID 3 to dziesięć sekund za TollID 1. 

**Dane wejściowe**:

| LicensePlate | Tworzenie | Time | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000Z | 3 |

**Dane wyjściowe**:

| TollID | Count |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Rozwiązanie**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Explanation**: [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) klauzuli patrzy na osi czasu każdego urządzenia, osobno przy użyciu substreams. Zdarzenia danych wyjściowych dla każdej TollID są generowane, ponieważ są one obliczane, co oznacza, że zdarzenia w kolejności, w odniesieniu do każdego TollID zamiast jest zmieniana tak, jakby wszystkie urządzenia były na tej samej zegara.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Przykład zapytania: Usuń zduplikowane zdarzenia w oknie

**Opis**: Podczas wykonywania operacji takich jak obliczanie wartości średnie zdarzeń w danym przedziale czasowym, zduplikowane zdarzenia powinny być filtrowane. W poniższym przykładzie drugie zdarzenie jest duplikatem pierwszego.

**Dane wejściowe**:  

| DeviceId | Time | Atrybut | Wartość |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Dane wyjściowe**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Rozwiązanie**:

```SQL
With Temp AS (
    SELECT
        COUNT(DISTINCT Time) AS CountTime,
        Value,
        DeviceId
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Value,
        DeviceId,
        SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Explanation**: [COUNT (DISTINCT czasu)](/stream-analytics-query/count-azure-stream-analytics) zwraca liczbę unikatowych wartości w kolumnie czas w przedziale czasu. Dane wyjściowe tego kroku można następnie użyć do obliczenia średniej na urządzeniu przez odrzucenie duplikatów.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

