---
title: Typowe wzorce zapytań w Azure Stream Analytics
description: W tym artykule opisano kilka typowych wzorców zapytań i projektów, które są przydatne w Azure Stream Analytics zadaniach.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/16/2019
ms.openlocfilehash: 61f9e128fa9299a743012e18882fe32591fdd3f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369953"
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Przykłady zapytań dla typowych wzorców użycia Stream Analytics

Zapytania w Azure Stream Analytics są wyrażane w języku zapytań przypominającym język SQL. Konstrukcje językowe są udokumentowane w podręczniku [Skorowidz języka zapytań Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference) . 

W projekcie zapytania można jasno utworzyć prostą logikę przekazującą, aby przenieść dane zdarzenia z jednego strumienia wejściowego do wyjściowego magazynu danych. może to spowodować rozbudowane dopasowanie wzorców i analizy czasowej w celu obliczenia agregacji w różnych oknach czasu, jak w przypadku [tworzenia rozwiązania IoT za pomocą](stream-analytics-build-an-iot-solution-using-stream-analytics.md) przewodnika Stream Analytics. Można przyłączyć dane z wielu wejść do łączenia zdarzeń przesyłania strumieniowego, a także odszukać statyczne dane referencyjne w celu wzbogacania wartości zdarzeń. Możesz również zapisywać dane w wielu danych wyjściowych.

W tym artykule przedstawiono rozwiązania kilku typowych wzorców zapytań w oparciu o rzeczywiste scenariusze.

## <a name="work-with-complex-data-types-in-json-and-avro"></a>Praca ze złożonymi typami danych w formatach JSON i AVRO

Azure Stream Analytics obsługuje przetwarzanie zdarzeń w formatach danych CSV, JSON i Avro.

Zarówno kod JSON, jak i Avro mogą zawierać złożone typy, takie jak obiekty zagnieżdżone (rekordy) lub tablice. Aby uzyskać więcej informacji na temat pracy z tymi złożonymi typami danych, zapoznaj się z artykułem [Analizowanie danych JSON i Avro](stream-analytics-parsing-json.md) .

## <a name="query-example-convert-data-types"></a>Przykład zapytania: konwertowanie typów danych

**Opis**: Zdefiniuj typy właściwości w strumieniu wejściowym. Na przykład waga samochodu odbywa się w strumieniu wejściowym jako ciągi i musi być konwertowana na **typ int** , aby wykonać **sumę**.

**Dane wejściowe**:

| Tworzenie | Czas | Waga |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000 Z |"2000" |

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

**Wyjaśnienie**: Użyj instrukcji **Cast** w polu **waga** , aby określić jej typ danych. Zapoznaj się z listą obsługiwanych typów danych w [typach danych (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Przykład zapytania: Użyj LIKE/NOT LIKE do dopasowania wzorca

**Opis**: Sprawdź, czy wartość pola w zdarzeniu jest zgodna z określonym wzorcem.
Na przykład sprawdź, czy wynik zwraca tablice licencji, które zaczynają się od i kończą się od 9.

**Dane wejściowe**:

| Tworzenie | LicensePlate | Czas |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Dane wyjściowe**:

| Tworzenie | LicensePlate | Czas |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000 Z |

**Rozwiązanie**:

```SQL
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'
```

**Wyjaśnienie**: Użyj instrukcji **like** , aby sprawdzić wartość pola **LicensePlate** . Powinna zaczynać się od litery A, a następnie zawierać dowolny ciąg z zero lub więcej znaków, a następnie kończyć się cyfrą 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Przykład zapytania: Określanie logiki dla różnych przypadków/wartości (instrukcje CASE)

**Opis**: podaj inne obliczenie dla pola w oparciu o określone kryterium. Na przykład Podaj opis ciągu, ile samochodów tego samego zakończono, z specjalnym przypadkiem 1.

**Dane wejściowe**:

| Tworzenie | Czas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Dane wyjściowe**:

| CarsPassed | Czas |
| --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000 Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000 Z |

**Rozwiązanie**:

```SQL
    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp() AS AsaTime
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
```

**Wyjaśnienie**: wyrażenie **Case** porównuje wyrażenie z zestawem prostych wyrażeń, aby określić wynik. W tym przykładzie nastąpi przekroczenie przez pojazd liczby 1 zwróconej przez inną liczbę znaków niż 1.

## <a name="query-example-send-data-to-multiple-outputs"></a>Przykład zapytania: wysyłanie danych do wielu wyjść

**Opis**: wysyłanie danych do wielu elementów docelowych danych wyjściowych z jednego zadania. Na przykład Analizuj dane dla alertu opartego na progach i Archiwizuj wszystkie zdarzenia w usłudze BLOB Storage.

**Dane wejściowe**:

| Tworzenie | Czas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Output1**:

| Tworzenie | Czas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Output2**:

| Tworzenie | Czas | Liczba |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000 Z |3 |

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
        System.TimeStamp() AS AsaTime,
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

**Wyjaśnienie**: klauzula **into** informuje Stream Analytics, do których danych wyjściowych chcesz zapisać dane z tej instrukcji. Pierwsze zapytanie jest przekazywane do danych wyjściowych o nazwie **ArchiveOutput**. Drugie zapytanie wykonuje prostą agregację i filtrowanie, a następnie wysyła wyniki do systemu alertów podrzędnych, **AlertOutput**.

Należy zauważyć, że można również ponownie wykorzystać wyniki wspólnych wyrażeń tabel (cyklicznych) (takich **jak instrukcje)** w wielu instrukcjach wyjściowych. Ta opcja umożliwia dodanie mniejszej liczby czytelników do źródła danych wejściowych.

Przykład: 

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

## <a name="query-example-count-unique-values"></a>Przykład zapytania: Zlicz unikatowe wartości

**Opis**: określa liczbę unikatowych wartości pól, które pojawiają się w strumieniu w przedziale czasu. Na przykład ile unikatowych ilości samochodów jest przenoszona przez połączenie płatne w 2-sekundowym oknie?

**Dane wejściowe**:

| Tworzenie | Czas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Honda |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |
| Toyota |2015-01-01T00:00:03.0000000 Z |

**Dane wyjściowe:**

| CountMake | Czas |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Rozwiązanie:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP() AS AsaTIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```


**Wyjaśnienie:** **Liczba
(różne marki)** zwraca liczbę unikatowych wartości w kolumnie **Utwórz** w przedziale czasu.

## <a name="query-example-determine-if-a-value-has-changed"></a>Przykład zapytania: Określanie, czy zmieniono wartość

**Opis**: Sprawdź poprzednią wartość, aby określić, czy jest różna od bieżącej wartości. Czy na przykład jest to poprzedni samochód na trasie, tak samo jak w przypadku bieżącego samochodu?

**Dane wejściowe**:

| Tworzenie | Czas |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |
| Toyota |2015-01-01T00:00:02.0000000 Z |

**Dane wyjściowe**:

| Tworzenie | Czas |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000 Z |

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

**Wyjaśnienie**: Użyj **opóźnienia** , aby przejść do strumienia wejściowego po jednym zdarzeniu i pobrać wartość **Utwórz** . Następnie porównaj ją z wartością **Utwórz** w bieżącym zdarzeniu i Wyprowadź zdarzenie, jeśli są różne.

## <a name="query-example-find-the-first-event-in-a-window"></a>Przykład zapytania: Znajdowanie pierwszego zdarzenia w oknie

**Opis**: Znajdź pierwszy samochód w każdym 10-minutowym przedziale czasu.

**Dane wejściowe**:

| LicensePlate | Tworzenie | Czas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015 — 07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Dane wyjściowe**:

| LicensePlate | Tworzenie | Czas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Honda |2015 — 07-27T00:12:02.0000000 Z |

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

Teraz Zmieńmy ten problem i znajdziesz pierwszy samochód z określonej marki w każdym 10-minutowym przedziale czasu.

| LicensePlate | Tworzenie | Czas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Honda |2015 — 07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

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

## <a name="query-example-find-the-last-event-in-a-window"></a>Przykład zapytania: Znajdowanie ostatniego zdarzenia w oknie

**Opis**: Znajdź ostatni samochód w każdym 10-minutowym przedziale czasu.

**Dane wejściowe**:

| LicensePlate | Tworzenie | Czas |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Honda |2015 — 07-27T00:12:02.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

**Dane wyjściowe**:

| LicensePlate | Tworzenie | Czas |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000 Z |

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

**Wyjaśnienie**: w zapytaniu istnieją dwa etapy. Pierwsza z nich znajduje najnowszą sygnaturę czasową w 10-minutowych oknach. Drugi krok sprzęga wyniki pierwszego zapytania z oryginalnym strumieniem, aby znaleźć zdarzenia zgodne z ostatnimi sygnaturami czasowymi w poszczególnych oknach. 

## <a name="query-example-locate-correlated-events-in-a-stream"></a>Przykład zapytania: Lokalizowanie skorelowanych zdarzeń w strumieniu

**Opis**: Znajdź skorelowane zdarzenia w strumieniu. Na przykład, jeśli w ciągu ostatnich 90 sekund nastąpiło połączenie z 2 kolejnymi samochodami z tej samej marki?

**Dane wejściowe**:

| Tworzenie | LicensePlate | Czas |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000 Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000 Z |

**Dane wyjściowe**:

| Tworzenie | Czas | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000 Z |

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

**Wyjaśnienie**: Użyj **opóźnienia** , aby przejść do strumienia wejściowego po jednym zdarzeniu i pobrać wartość **Utwórz** . Porównaj ją z wartością **Make** w bieżącym zdarzeniu, a następnie Wyprowadź zdarzenie, jeśli są takie same. Możesz również użyć **zwłoki** , aby pobrać dane dotyczące poprzedniego samochodu.

## <a name="query-example-detect-the-duration-between-events"></a>Przykład zapytania: wykrywanie czasu trwania między zdarzeniami

**Opis**: Znajdź czas trwania danego zdarzenia. Na przykład, uwzględniając strumienia kliknięć sieci Web, określ czas spędzony na funkcji.

**Dane wejściowe**:  

| Użytkownik | Funkcja | Wydarzenie | Czas |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Rozpocznij |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Koniec |2015-01-01T00:00:08.0000000 Z |

**Dane wyjściowe**:  

| Użytkownik | Funkcja | Czas trwania |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Rozwiązanie**:

```SQL
    SELECT
        [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
```

**Wyjaśnienie**: Użyj **ostatniej** funkcji, aby pobrać ostatnią wartość **czasu** , gdy typ zdarzenia został **uruchomiony**. **Ostatnia** funkcja używa **partycji przez [użytkownika]** , aby wskazać, że wynik jest obliczany dla unikatowego użytkownika. W zapytaniu obowiązuje maksymalny próg czasu między zdarzeniami **uruchamiania** i **zatrzymywania** , ale można go skonfigurować zgodnie z wymaganiami **(czas trwania (godzina, 1)** .

## <a name="query-example-detect-the-duration-of-a-condition"></a>Przykład zapytania: wykrywanie czasu trwania warunku
**Opis**: Dowiedz się, jak długo wystąpił warunek.
Załóżmy na przykład, że usterka spowodowała, że wszystkie samochody mają niepoprawną wagę (powyżej 20 000 funtów), a czas trwania tego błędu musi być obliczany.

**Dane wejściowe**:

| Tworzenie | Czas | Waga |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000 Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000 Z |25000 |
| Honda |2015-01-01T00:00:03.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000 Z |25000 |
| Honda |2015-01-01T00:00:05.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000 Z |25000 |
| Honda |2015-01-01T00:00:07.0000000 Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000 Z |2000 |

**Dane wyjściowe**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

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

**Wyjaśnienie**: Użyj **opóźnienia** , aby wyświetlić strumień wejściowy przez 24 godziny i wyszukać wystąpienia, w których **StartFault** i **StopFault** są objęte wagami < 20000.

## <a name="query-example-fill-missing-values"></a>Przykład zapytania: wypełnienie brakujące wartości

**Opis**: w przypadku strumienia zdarzeń, które mają brakujące wartości, program tworzy strumień zdarzeń z regularnymi interwałami. Na przykład Wygeneruj zdarzenie co 5 sekund, które raportuje ostatnio widziany punkt danych.

**Dane wejściowe**:

| t | wartość |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Dane wyjściowe (pierwsze 10 wierszy)** :

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10.000 Z |2014-01-01T14:01:10.000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Rozwiązanie**:

```SQL
    SELECT
        System.Timestamp() AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)
```

**Wyjaśnienie**: to zapytanie generuje zdarzenia co 5 sekund i wyświetla ostatnie zdarzenie, które zostało wcześniej odebrane. Czas trwania [okna przeskoku](/stream-analytics-query/hopping-window-azure-stream-analytics) określa, jak daleko z tyłu zapytanie szuka najnowszego zdarzenia (300 sekund w tym przykładzie).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Przykład zapytania: skorelowanie dwóch typów zdarzeń w ramach tego samego strumienia

**Opis**: czasami alerty muszą być generowane na podstawie wielu typów zdarzeń, które wystąpiły w określonym przedziale czasu. Na przykład w scenariuszu IoT dla piekarników domowych należy wygenerować alert, gdy temperatura wentylatora jest mniejsza niż 40, a maksymalna moc w ciągu ostatnich 3 minut jest mniejsza niż 10.

**Dane wejściowe**:

| time | deviceId | sensorName | wartość |
| --- | --- | --- | --- |
| "2018-01-01T16:01:00" | "Oven1" | temp |120 |
| "2018-01-01T16:01:00" | "Oven1" | awansowan |15 |
| "2018-01-01T16:02:00" | "Oven1" | temp |100 |
| "2018-01-01T16:02:00" | "Oven1" | awansowan |15 |
| "2018-01-01T16:03:00" | "Oven1" | temp |70 |
| "2018-01-01T16:03:00" | "Oven1" | awansowan |15 |
| "2018-01-01T16:04:00" | "Oven1" | temp |50 |
| "2018-01-01T16:04:00" | "Oven1" | awansowan |15 |
| "2018-01-01T16:05:00" | "Oven1" | temp |30 |
| "2018-01-01T16:05:00" | "Oven1" | awansowan |8 |
| "2018-01-01T16:06:00" | "Oven1" | temp |20 |
| "2018-01-01T16:06:00" | "Oven1" | awansowan |8 |
| "2018-01-01T16:07:00" | "Oven1" | temp |20 |
| "2018-01-01T16:07:00" | "Oven1" | awansowan |8 |
| "2018-01-01T16:08:00" | "Oven1" | temp |20 |
| "2018-01-01T16:08:00" | "Oven1" | awansowan |8 |

**Dane wyjściowe**:

| eventTime | deviceId | temp | Określony komunikat alarmu | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| "2018-01-01T16:05:00" | "Oven1" |30 | "Elementy ogrzewania dla krótkich obwodów" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Elementy ogrzewania dla krótkich obwodów" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Elementy ogrzewania dla krótkich obwodów" |15 |

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

**Wyjaśnienie**: pierwsze zapytanie `max_power_during_last_3_mins`, używa [przesuwanego okna](/stream-analytics-query/sliding-window-azure-stream-analytics) , aby znaleźć maksymalną wartość czujnika napięcia dla każdego urządzenia, w ciągu ostatnich 3 minut. Drugie zapytanie jest przyłączone do pierwszego zapytania, aby znaleźć wartość mocy w najnowszym oknie odpowiednim dla bieżącego zdarzenia. A następnie, pod warunkiem, że są spełnione, generowany jest alert dla urządzenia.

## <a name="query-example-process-events-independent-of-device-clock-skew-substreams"></a>Przykład zapytania: przetwarzanie zdarzeń niezależnie od przesunięcia zegara urządzenia (podstrumienia)

**Opis**: zdarzenia mogą być opóźnione lub nieaktualne z powodu pochylenia zegara między producentami zdarzeń, nachylenia zegara między partycjami lub opóźnienia sieci. W poniższym przykładzie zegar urządzenia dla TollID 2 ma pięć sekund w tle TollID 1, a zegar urządzenia dla TollID 3 to dziesięć sekund za TollID 1. 

**Dane wejściowe**:

| LicensePlate | Tworzenie | Czas | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Toyota |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Honda |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |BMW |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Toyota |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Honda |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |BMW |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Ford |2015-07-27T00:00:07.0000000 Z | 3 |

**Dane wyjściowe**:

| TollID | Liczba |
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

**Wyjaśnienie**: klauzula [timestamp by over](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering) wyszukuje osobno każdy oś czasu za pomocą podstrumienia. Zdarzenia wyjściowe dla każdego TollID są generowane w miarę ich obliczania, co oznacza, że zdarzenia są wykonywane w kolejności w odniesieniu do każdego TollIDu, a nie w kolejności, w jakiej wszystkie urządzenia były w tym samym zegarze.

## <a name="query-example-remove-duplicate-events-in-a-window"></a>Przykład zapytania: usuwanie zduplikowanych zdarzeń w oknie

**Opis**: podczas wykonywania operacji, takich jak Obliczanie średniej wartości dla zdarzeń w danym przedziale czasu, należy filtrować zduplikowane zdarzenia. W poniższym przykładzie drugie zdarzenie jest duplikatem pierwszego.

**Dane wejściowe**:  

| DeviceId | Czas | Atrybut | Wartość |
| --- | --- | --- | --- |
| 1 |2018 R-07-27T00:00:01.0000000 Z |Temperatura |50 |
| 1 |2018 R-07-27T00:00:01.0000000 Z |Temperatura |50 |
| 2 |2018 R-07-27T00:00:01.0000000 Z |Temperatura |40 |
| 1 |2018 R-07-27T00:00:05.0000000 Z |Temperatura |60 |
| 2 |2018 R-07-27T00:00:05.0000000 Z |Temperatura |50 |
| 1 |2018 R-07-27T00:00:10.0000000 Z |Temperatura |100 |

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

**Wyjaśnienie**: [licznik (DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics) zwraca liczbę unikatowych wartości w kolumnie Time w przedziale czasu. Następnie możesz użyć danych wyjściowych tego kroku, aby obliczyć średnią dla każdego urządzenia przez odrzucenie duplikatów.

## <a name="geofencing-and-geospatial-queries"></a>Geoogrodzenie i zapytania geograficzne
Azure Stream Analytics udostępnia wbudowane funkcje geoprzestrzenne, które mogą służyć do implementowania scenariuszy takich jak zarządzanie flotą, udostępnianie samochodu, samochody połączone i śledzenie zasobów. Dane geograficzne można pozyskać w formatach GeoJSON lub WKT w ramach strumienia zdarzeń lub danych referencyjnych. Aby uzyskać więcej informacji, zapoznaj się z [scenariuszami geoprzestrzennymi i agregacjami geograficznymi przy użyciu Azure Stream Analytics](geospatial-scenarios.md) artykułu.

## <a name="language-extensibility-through-javascript-and-c"></a>Rozszerzalność języka za poorednictwem języka JavaScript iC#
Usługę Azure Stream Ananlytics Query langugae można rozszerzyć za pomocą funkcji niestandardowych zapisanych w C# języku JavaScript lub językach. Aby uzyskać więcej informacji, zobacz artykuły z foolowing:
* [Azure Stream Analytics funkcje języka JavaScript zdefiniowane przez użytkownika](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics kodu JavaScript zdefiniowanych przez użytkownika](stream-analytics-javascript-user-defined-aggregates.md)
* [Opracowywanie .NET Standard funkcji zdefiniowanych przez użytkownika dla zadań Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md)

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

