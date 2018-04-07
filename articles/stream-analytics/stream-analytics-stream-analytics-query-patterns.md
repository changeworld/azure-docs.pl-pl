---
title: Typowych wzorców zapytań w programie Azure Stream Analytics
description: W tym artykule opisano kilka typowych wzorców zapytań i projekty, które są przydatne w zadania usługi analiza strumienia Azure.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: b929eaf17255210a5c813e3e91478f9202941b64
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Zapytanie przykłady typowych wzorców użycia usługi analiza strumienia
## <a name="introduction"></a>Wprowadzenie
Zapytania w usłudze Azure Stream Analytics są wyrażone według języka przypominającego SQL zapytań. Te zapytania są udokumentowane w artykule [materiały referencyjne dotyczące języka zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) przewodnik. W tym artykule przedstawiono rozwiązania kilka typowych wzorców zapytań, na podstawie w rzeczywistych scenariuszach. Jest pracy w toku i jest aktualizowany o nowe wzorce w sposób ciągły.

## <a name="query-example-convert-data-types"></a>Przykład zapytania: konwersji typów danych
**Opis elementu**: Definiowanie typów właściwości ze strumienia wejściowego.
Na przykład wagi samochodu pochodzi ze strumienia wejściowego jako ciągi i musi zostać skonwertowany do **INT** przeprowadzić **suma** go.

**Dane wejściowe**:

| Wprowadź | Time | Waga |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Dane wyjściowe**:

| Wprowadź | Waga |
| --- | --- |
| Honda |3000 |

**Rozwiązanie**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Wyjaśnienie**: Użyj **RZUTOWANIA** instrukcji w **wagi** pola, aby określić typ jej danych. Zobacz listę obsługiwanych typów danych w [typy danych (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Przykład zapytania: Użyj notacji nie zostać dopasowanie wzorca
**Opis elementu**: Sprawdź, czy wartość pola o zdarzeniu zgodna niektórych wzorca.
Na przykład sprawdzić, czy wynik zwraca płyt licencji, które A zaczynać się i kończyć 9.

**Dane wejściowe**:

| Wprowadź | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Dane wyjściowe**:

| Wprowadź | LicensePlate | Time |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Rozwiązanie**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Wyjaśnienie**: Użyj **jak** instrukcji, aby sprawdzić **LicensePlate** wartość w polu. Powinna zaczynać A, a następnie mieć dowolny ciąg zawierający zero lub więcej znaków i następnie kończyć 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Przykład zapytania: Określ logikę różnych przypadków/wartości (instrukcji CASE)
**Opis elementu**: Podaj innej obliczania pola, na podstawie określonego kryterium.
Na przykład Podaj opis ciągu upewnij ile samochodów tego samego przekazany z szczególnych przypadkach 1.

**Dane wejściowe**:

| Wprowadź | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Dane wyjściowe**:

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Rozwiązanie**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Wyjaśnienie**: **przypadku** klauzuli pozwala na udostępnianie różnych obliczeń, na podstawie kryteriów, niektóre (w tym przypadku liczba samochodów w oknie agregacji).

## <a name="query-example-send-data-to-multiple-outputs"></a>Przykład zapytania: wysyłanie danych do wielu wyjść
**Opis elementu**: wysyłania danych do wielu elementów docelowych w danych wyjściowych z jednym zadaniu.
Na przykład analizować dane oparte na wartościach progowych alertu oraz archiwum wszystkie zdarzenia do magazynu obiektów blob.

**Dane wejściowe**:

| Wprowadź | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Wprowadź | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Wprowadź | Time | Licznik |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Rozwiązanie**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
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

**Wyjaśnienie**: **INTO** klauzuli informuje Stream Analytics której dane wyjściowe można zapisać danych do tej instrukcji.
Pierwszego zapytania jest przekazywanie danych Otrzymaliśmy do wyjścia nasze konto nazywa się **ArchiveOutput**.
Drugiego zapytania jest niektórych prostych agregacji i filtrowanie i wysyła wyniki do podrzędne system alertów.

Należy pamiętać, że możesz również użyć wyniki wspólnych wyrażeniach tabel (wyrażeń CTE) (takich jak **WITH** instrukcje) w wielu deklaracjach danych wyjściowych. Ta opcja ma dodatkowa korzyść otwarcia mniej czytników do źródła danych wejściowych.
Na przykład: 

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

## <a name="query-example-count-unique-values"></a>Przykład zapytania: liczba unikatowych wartości
**Opis elementu**: liczbę unikatowych wartości pól wyświetlanych w strumieniu w przedziale czasu.
Na przykład ile unikatowy sprawia, że przekazywane przez kabinę w oknie 2 sekundy samochodów?

**Dane wejściowe**:

| Wprowadź | Time |
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

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Wyjaśnienie:**
**COUNT (różne upewnij)** zwraca liczbę unikatowych wartości w **upewnij** kolumny w przedziale czasu.

## <a name="query-example-determine-if-a-value-has-changed"></a>Przykład zapytania: ustalić, jeśli wartość została zmieniona
**Opis elementu**: przyjrzeć się poprzedniej wartości, aby ustalić, czy jest inna niż bieżąca wartość.
Na przykład jest poprzedniej samochodu na drodze przez tego samego upewnij jako bieżący samochód?

**Dane wejściowe**:

| Wprowadź | Time |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Dane wyjściowe**:

| Wprowadź | Time |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Rozwiązanie**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Wyjaśnienie**: Użyj **LAG** wgląd do strumienia wejściowego jednego zdarzenia Wstecz w celu uzyskania **upewnij** wartość. Następnie porównania jej **upewnij** wartość dla bieżącego zdarzenia i dane wyjściowe zdarzenia, jeśli są one różne.

## <a name="query-example-find-the-first-event-in-a-window"></a>Przykład zapytania: Znajdź pierwsze zdarzenie w oknie
**Opis elementu**: Znajdź pierwszego samochodu co 10 minut.

**Dane wejściowe**:

| LicensePlate | Wprowadź | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Dane wyjściowe**:

| LicensePlate | Wprowadź | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Rozwiązanie**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Teraz załóżmy zmienić problemu i Znajdź pierwszego samochodu marki określonego w co 10 minut.

| LicensePlate | Wprowadź | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Rozwiązanie**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Przykład zapytania: Znajdź ostatnie zdarzenie w oknie
**Opis elementu**: Znajdź ostatni samochodu co 10 minut.

**Dane wejściowe**:

| LicensePlate | Wprowadź | Time |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Dane wyjściowe**:

| LicensePlate | Wprowadź | Time |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Rozwiązanie**:

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

**Wyjaśnienie**: istnieją dwa kroki w zapytaniu. Pierwsza z nich znajduje najnowsze sygnatury czasowej w systemie windows 10 minut. Drugim krokiem łączy wyniki pierwszego zapytania z oryginalnego strumienia do znalezienia zdarzeń, zgodne ostatniego sygnatury czasowe w każdym okna. 

## <a name="query-example-detect-the-absence-of-events"></a>Przykład zapytania: wykrycia braku zdarzeń
**Opis elementu**: Sprawdź, czy strumień nie ma wartości odpowiadający niektórych kryterium.
Na przykład 2 samochodów następujących po sobie z tym samym upewnij wprowadzony drogowej przez w ciągu ostatnich 90 sekund?

**Dane wejściowe**:

| Wprowadź | LicensePlate | Time |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Dane wyjściowe**:

| Wprowadź | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Rozwiązanie**:

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

**Wyjaśnienie**: Użyj **LAG** wgląd do strumienia wejściowego jednego zdarzenia Wstecz w celu uzyskania **upewnij** wartość. Aby porównać **upewnij** wartość w bieżącym zdarzeń, a następnie dane wyjściowe zdarzenia, jeśli są one takie same. Można również użyć **LAG** można pobrać danych dotyczących samochodów poprzedniej.

## <a name="query-example-detect-the-duration-between-events"></a>Przykład zapytania: wykrywanie czas między zdarzeniami
**Opis elementu**: Znajdź czasu trwania jednego z określonych zdarzeń. Biorąc pod uwagę clickstream sieci web, na przykład określić czas spędzony na funkcji.

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

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Wyjaśnienie**: Użyj **ostatniego** funkcji, aby pobrać ostatniego **czasu** wartość, gdy typ zdarzenia **Start**. **Ostatniego** używa **PARTITION BY [użytkownik]** wskazująca, czy wynik jest obliczana na unikatowy użytkownika. Zapytanie ma 1 godzina próg maksymalnego różnica czasu między **Start** i **zatrzymać** zdarzenia, ale można skonfigurować zgodnie z potrzebami **(LIMIT DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Przykład zapytania: wykrywanie w czasie trwania warunek
**Opis elementu**: Sprawdzanie, jak długo wystąpił warunek.
Na przykład załóżmy, że usterki spowodowała wszystkich samochodów niepoprawne ciężar (ponad 20 000 jednostkach funt). Chcemy obliczeniowe czas trwania usterki.

**Dane wejściowe**:

| Wprowadź | Time | Waga |
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

````
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
````

**Wyjaśnienie**: Użyj **LAG** do wyświetlania strumień wejściowy przez 24 godziny i poszukaj wystąpień where **StartFault** i **StopFault** są łączone za pomocą wag < 20000.

## <a name="query-example-fill-missing-values"></a>Przykład zapytania: wypełnienie brakujących wartości
**Opis elementu**: dla tego strumienia zdarzeń, które nie mają wartości tworzy strumień zdarzeń o regularnych odstępach czasu.
Na przykład generują zdarzenie co 5 sekund, która raportuje najbardziej ostatnio widziany punktu danych.

**Dane wejściowe**:

| t | wartość |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Dane wyjściowe (pierwszych 10 wierszy)**:

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

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Wyjaśnienie**: to zapytanie generuje zdarzenia co 5 sekund i wyprowadza ostatnie zdarzenie odebrany wcześniej. [Okna Hopping](https://msdn.microsoft.com/library/dn835041.aspx "Hopping okno usługi Azure Stream Analytics") czas trwania określa, jak daleko wstecz zapytanie odwołuje się do Znajdź najnowsze zdarzenie (300 sekund w tym przykładzie).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Przykład zapytania: skorelowania dwa typy zdarzeń, w ramach tego samego strumienia
**Opis elementu**: Czasami musimy generować alerty oparte na wiele typów zdarzeń, które wystąpiły w zakresie czasu.
Na przykład w scenariuszu IoT dla piekarników macierzystego, chcemy Zgłoś alert, gdy temperatury wentylator jest mniejsza niż 40 i maksymalną moc w ciągu ostatnich 3 minut była mniejsza niż 10.

**Dane wejściowe**:

| time | deviceId | sensorName | wartość |
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
| "2018-01-01T16:05:00" | "Oven1" |30 | "Obwód krótkiej elementy grzewcze" |15 |
| "2018-01-01T16:06:00" | "Oven1" |20 | "Obwód krótkiej elementy grzewcze" |15 |
| "2018-01-01T16:07:00" | "Oven1" |20 | "Obwód krótkiej elementy grzewcze" |15 |

**Rozwiązanie**:

````
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
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
````

**Wyjaśnienie**: pierwsza kwerenda `max_power_during_last_3_mins`, używa [okna ruchomej](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) można znaleźć maksymalną wartość czujnika zasilania dla każdego urządzenia w ciągu ostatnich 3 minut. Drugiego zapytania jest dołączony do pierwszego zapytania, aby znaleźć wartość zasilania w oknie najnowszych odpowiednie dla bieżącego zdarzenia. A następnie, pod warunkiem warunki są spełnione, alert zostanie wygenerowany dla urządzenia.


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

