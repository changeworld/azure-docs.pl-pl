---
title: Typowe wzorce zapytań w usłudze Azure Stream Analytics
description: W tym artykule opisano kilka typowych wzorców zapytań i projektów, które są przydatne w zadaniach usługi Azure Stream Analytics.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: aa8bd6e89dd47c4e972a860691d1bc3779ba5bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982310"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Typowe wzorce zapytań w usłudze Azure Stream Analytics

Zapytania w usłudze Azure Stream Analytics są wyrażone w języku zapytań podobnych do SQL. Konstrukcje języka są udokumentowane w przewodniku [odwzłacza języka zapytań usługi Stream Analytics.](/stream-analytics-query/stream-analytics-query-language-reference) 

Projekt kwerendy można wyrazić prostą logikę przekazywania, aby przenieść dane zdarzeń z jednego strumienia wejściowego do magazynu danych wyjściowych lub można wykonać dopasowanie wzorca bogatego i analizy czasowej do obliczania agregatów w różnych oknach czasu, jak w [kompilacji rozwiązania IoT przy użyciu przewodnika Usługi Stream Analytics.](stream-analytics-build-an-iot-solution-using-stream-analytics.md) Można dołączyć dane z wielu danych wejściowych, aby połączyć zdarzenia przesyłania strumieniowego i można wykonać wyszukiwania względem statycznych danych referencyjnych, aby wzbogacić wartości zdarzeń. Można również zapisywać dane do wielu wyjść.

W tym artykule przedstawiono rozwiązania do kilku typowych wzorców zapytań na podstawie rzeczywistych scenariuszy.

## <a name="supported-data-formats"></a>Obsługiwane formaty danych

Usługa Azure Stream Analytics obsługuje przetwarzanie zdarzeń w formatach danych CSV, JSON i Avro.

Zarówno JSON, jak i Avro mogą zawierać złożone typy, takie jak zagnieżdżone obiekty (rekordy) lub tablice. Aby uzyskać więcej informacji na temat pracy z tych złożonych typów danych, zobacz [analizowanie JSON i AVRO](stream-analytics-parsing-json.md) data article.

## <a name="simple-pass-through-query"></a>Proste zapytanie przekazywane

Proste zapytanie przekazywane może służyć do kopiowania danych strumienia wejściowego do danych wyjściowych. Na przykład jeśli strumień danych zawierających informacje o pojeździe w czasie rzeczywistym musi zostać zapisany w bazie danych SQL do analizy listów, proste zapytanie przekazywane wykona zadanie.

**Wejście**:

| Marka | Time | Waga |
| --- | --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |"2000" |

**Wyjście:**

| Marka | Time | Waga |
| --- | --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |"2000" |

**Zapytanie**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

Kwerenda **SELECT** * projektuje wszystkie pola zdarzenia przychodzącego i wysyła je do danych wyjściowych. W ten sam sposób **select** może być również używany do projekcji tylko wymagane pola z danych wejściowych. W tym przykładzie, jeśli polecenie *Make* and *Time* jest jedynym wymaganym polem do zapisania, pola te można określić w instrukcji **SELECT.**

**Wejście**:

| Marka | Time | Waga |
| --- | --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 (Make2) |2015-01-01T00:00:04.0000000Z |1500 |

**Wyjście:**

| Marka | Time |
| --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:04.0000000Z |

**Zapytanie**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```
## <a name="data-aggregation-over-time"></a>Agregacja danych w czasie

Aby obliczyć informacje w danym oknie czasu, dane mogą być agregowane razem. W tym przykładzie liczba jest obliczany w ciągu ostatnich 10 minut czasu dla każdego konkretnego samochodu zrobić.

**Wejście**:

| Marka | Time | Waga |
| --- | --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |1000 |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |2000 |
| Make2 (Make2) |2015-01-01T00:00:04.0000000Z |1500 |

**Wyjście:**

| Marka | Liczba |
| --- | --- |
| Make1 (Dokonać) | 2 |
| Make2 (Make2) | 1 |

**Zapytanie**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Ta agregacja grupuje samochody według *Make* i liczy je co 10 sekund. Wyjście ma *Make* and *Count* samochodów, które przeszły przez opłaty drogowe.

TumblingWindow jest funkcją okna używane do grupowania zdarzeń razem. Agregacja może być stosowana we wszystkich zgrupowanych zdarzeniach. Aby uzyskać więcej informacji, zobacz [funkcje okna](stream-analytics-window-functions.md).

Aby uzyskać więcej informacji na temat agregacji, zapoznaj się z [funkcjami agreguuu .](/stream-analytics-query/aggregate-functions-azure-stream-analytics)

## <a name="data-conversion"></a>Konwersja danych

Dane mogą być rzutowe w czasie rzeczywistym przy użyciu **metody CAST.** Na przykład masę samochodu można przekonwertować z typu **nvarchar(max)** na typ **bigint** i być używana w obliczeniach liczbowych.

**Wejście**:

| Marka | Time | Waga |
| --- | --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |"1000" |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |"2000" |

**Wyjście:**

| Marka | Waga |
| --- | --- |
| Make1 (Dokonać) |3000 |

**Zapytanie**:

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

Użyj instrukcji **CAST,** aby określić jej typ danych. Zobacz listę obsługiwanych typów danych w [typach danych (Usługa Azure Stream Analytics).](/stream-analytics-query/data-types-azure-stream-analytics)

Aby uzyskać więcej informacji na temat [funkcji konwersji danych](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="string-matching-with-like-and-not-like"></a>Dopasowanie ciągów z LIKE i NOT LIKE

**LIKE** i **NOT LIKE** może służyć do sprawdzenia, czy pole pasuje do określonego wzorca. Na przykład można utworzyć filtr, aby zwrócić tylko tablice rejestracyjne, które zaczynają się od litery "A" i kończą na numerze 9.

**Wejście**:

| Marka | License_plate | Time |
| --- | --- | --- |
| Make1 (Dokonać) |Abc-123 |2015-01-01T00:00:01.0000000Z |
| Make2 (Make2) |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 (Make3) |Abc-369 |2015-01-01T00:00:03.0000000Z |

**Wyjście:**

| Marka | License_plate | Time |
| --- | --- | --- |
| Make2 (Make2) |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make3 (Make3) |Abc-369 |2015-01-01T00:00:03.0000000Z |

**Zapytanie**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Instrukcja **LIKE** służy do sprawdzania wartości pola **License_plate.** Powinien zaczynać się od litery "A", a następnie mieć dowolny ciąg znaków zero lub więcej, kończąc na numerze 9.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Określanie logiki dla różnych przypadków/wartości (instrukcje CASE)

**Instrukcje CASE** mogą dostarczać różnych obliczeń dla różnych pól, na podstawie określonego kryterium. Na przykład przypisz pas "A" do samochodów *Typu Make1* i pas "B" do każdej innej produkcji.

**Wejście**:

| Marka | Time |
| --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:03.0000000Z |

**Wyjście:**

| Marka |Dispatch_to_lane | Time |
| --- | --- | --- |
| Make1 (Dokonać) |"A" |2015-01-01T00:00:01.0000000Z |
| Make2 (Make2) |"B" |2015-01-01T00:00:02.0000000Z |

**Rozwiązanie 2.**

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

Wyrażenie **CASE** porównuje wyrażenie z zestawem prostych wyrażeń w celu określenia jego wyniku. W tym przykładzie pojazdy *Make1* są wysyłane do pasa "A", podczas gdy pojazdy innych czynów zostaną przypisane do pasa "B".

Aby uzyskać więcej informacji, zapoznaj się z [wyrażeniem sprawy](/stream-analytics-query/case-azure-stream-analytics).

## <a name="send-data-to-multiple-outputs"></a>Wysyłanie danych do wielu wyjść

Wiele **instrukcji SELECT** może służyć do wyprowadzania danych do różnych zlewów wyjściowych. Na przykład jeden **SELECT** można wyprowadzić alert oparty na progu, podczas gdy inny może wyprowadzać zdarzenia do magazynu obiektów blob.

**Wejście**:

| Marka | Time |
| --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:01.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:03.0000000Z |

**Wyjście ArchiveOutput**:

| Marka | Time |
| --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:01.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:03.0000000Z |

**Alert wyjściowyUchłna:**

| Marka | Time | Liczba |
| --- | --- | --- |
| Make2 (Make2) |2015-01-01T00:00:10.0000000Z |3 |

**Zapytanie**:

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

Into **INTO** Klauzula informuje usługi Stream Analytics, które z wyjść do zapisania danych. Pierwszy **SELECT** definiuje kwerendę przekazującą, która odbiera dane z danych wejściowych i wysyła je do danych wyjściowych o nazwie **ArchiveOutput**. Drugie zapytanie wykonuje kilka prostych agregacji i filtrowania przed wysłaniem wyników do niższego wyjścia systemu ostrzegania o nazwie **AlertOutput**.

Należy zauważyć, że **with** klauzuli może służyć do definiowania wielu bloków podduszu. Ta opcja ma tę zaletę, że można otworzyć mniej czytników do źródła wejściowego.

**Zapytanie**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Aby uzyskać więcej informacji, zapoznaj się [ **z klauzulą WITH** ](/stream-analytics-query/with-azure-stream-analytics).

## <a name="count-unique-values"></a>Zliczanie unikatowych wartości

**LICZBA** i **DISTINCT** może służyć do zliczania liczby unikatowych wartości pól, które pojawiają się w strumieniu w przedziale czasu. Można utworzyć zapytanie, aby obliczyć, ile unikalnych *samochodów* przeszło przez punkt poboru opłat w oknie 2-sekundowym.

**Wejście**:

| Marka | Time |
| --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:01.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:03.0000000Z |

**Dane wyjściowe:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Kwerendy:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** zwraca liczbę odrębnych wartości w kolumnie **Make** w przedziale czasu.
Aby uzyskać więcej informacji, zobacz [funkcja agregująca **COUNT** ](/stream-analytics-query/count-azure-stream-analytics).

## <a name="calculation-over-past-events"></a>Obliczanie zdarzeń z przeszłości

Funkcja **LGD** może służyć do analizowania przeszłych zdarzeń w przedziale czasu i porównywania ich z bieżącym zdarzeniem. Na przykład, obecna produkcja samochodu może być wyprowadzana, jeśli różni się od ostatniego samochodu, który przeszedł przez opłatę.

**Wejście**:

| Marka | Time |
| --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |
| Make2 (Make2) |2015-01-01T00:00:02.0000000Z |

**Wyjście:**

| Marka | Time |
| --- | --- |
| Make2 (Make2) |2015-01-01T00:00:02.0000000Z |

**Zapytanie**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Użyj **LAG,** aby zajrzeć do strumienia wejściowego jedno zdarzenie z powrotem, pobieranie *Make* wartość i porównując go do *Make* wartość bieżącego zdarzenia i wyjście zdarzenia.

Więcej informacji można znaleźć w [**LGD**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Pobieranie pierwszego zdarzenia w oknie

**IsFirst** może służyć do pobierania pierwszego zdarzenia w oknie czasu. Na przykład wyprowadzanie pierwszych informacji o samochodzie co 10 minut.

**Wejście**:

| License_plate | Marka | Time |
| --- | --- | --- |
| DXE 5291 |Make1 (Dokonać) |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (Make3) |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 (Dokonać) |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 (Make2) |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 (Make2) |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 (Dokonać) |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 (Make4) |2015-07-27T00:13:45.0000000Z |

**Wyjście:**

| License_plate | Marka | Time |
| --- | --- | --- |
| DXE 5291 |Make1 (Dokonać) |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Make1 (Dokonać) |2015-07-27T00:12:02.0000000Z |

**Zapytanie**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** może również partycjonować dane i obliczyć pierwsze zdarzenie do każdego konkretnego samochodu *Make* znaleźć co 10 minut interwału.

**Wyjście:**

| License_plate | Marka | Time |
| --- | --- | --- |
| DXE 5291 |Make1 (Dokonać) |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (Make3) |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Make2 (Make2) |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Make1 (Dokonać) |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 (Make4) |2015-07-27T00:13:45.0000000Z |

**Zapytanie**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Aby uzyskać więcej informacji, zobacz [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Zwracanie ostatniego zdarzenia w oknie

Ponieważ zdarzenia są używane przez system w czasie rzeczywistym, nie ma żadnej funkcji, która może określić, czy zdarzenie będzie ostatnim, które pojawi się w tym oknie czasu. Aby to osiągnąć, strumień wejściowy musi być połączony z innym, gdzie czas zdarzenia jest maksymalny czas dla wszystkich zdarzeń w tym oknie.

**Wejście**:

| License_plate | Marka | Time |
| --- | --- | --- |
| DXE 5291 |Make1 (Dokonać) |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Make3 (Make3) |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Make1 (Dokonać) |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Make2 (Make2) |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Make2 (Make2) |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Make1 (Dokonać) |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |Make4 (Make4) |2015-07-27T00:13:45.0000000Z |

**Wyjście:**

| License_plate | Marka | Time |
| --- | --- | --- |
| VFE 1616 |Make2 (Make2) |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |Make4 (Make4) |2015-07-27T00:13:45.0000000Z |

**Zapytanie**:

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
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

Pierwszy krok w kwerendzie znajduje maksymalną sygnaturę czasową w oknach 10-minutowych, czyli sygnaturę czasową ostatniego zdarzenia dla tego okna. Drugi krok łączy wyniki pierwszej kwerendy z oryginalnego strumienia, aby znaleźć zdarzenie, które pasują do ostatnich sygnatur czasowych w każdym oknie. 

**DATEDIFF** jest funkcją specyficzną dla daty, która porównuje i zwraca różnicę czasu między dwoma polami DateTime, aby uzyskać więcej informacji, zapoznaj się z [funkcjami daty](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Więcej informacji na temat dołączania do strumieni można znaleźć w [**łączeniu**](/stream-analytics-query/join-azure-stream-analytics).


## <a name="correlate-events-in-a-stream"></a>Skorelowanie zdarzeń w strumieniu

Skorelowanie zdarzeń w tym samym strumieniu można wykonać, patrząc na zdarzenia z przeszłości przy użyciu funkcji **LGD.** Na przykład wyjście może być generowane za każdym razem, gdy dwa kolejne samochody z tego samego *Make* przechodzą przez opłatę za przejazd przez ostatnie 90 sekund.

**Wejście**:

| Marka | License_plate | Time |
| --- | --- | --- |
| Make1 (Dokonać) |Abc-123 |2015-01-01T00:00:01.0000000Z |
| Make1 (Dokonać) |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Make2 (Make2) |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Make1 (Dokonać) |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Wyjście:**

| Marka | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:02.0000000Z |AAA-999 |Abc-123 |2015-01-01T00:00:01.0000000Z |

**Zapytanie**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Funkcja LGD** może zajrzeć do strumienia wejściowego jedno zdarzenie z powrotem i pobrać *Make* wartość, porównując, że z *Make* wartość bieżącego zdarzenia.  Po spełnieniu warunku dane z poprzedniego zdarzenia mogą być rzutowane przy użyciu **LGD** w instrukcji **SELECT.**

Więcej informacji można znaleźć w [LGD](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Wykrywanie czasu trwania zdarzeń

Czas trwania zdarzenia można obliczyć, patrząc na ostatnie zdarzenie Start po odebraniu zdarzenia End. Ta kwerenda może być przydatna do określenia czasu spędzanego przez użytkownika na stronie lub funkcji.

**Wejście**:  

| Użytkownik | Funkcja | Wydarzenie | Time |
| --- | --- | --- | --- |
| user@location.com |Właściwymenu |Rozpoczęcie |2015-01-01T00:00:01.0000000Z |
| user@location.com |Właściwymenu |End |2015-01-01T00:00:08.0000000Z |

**Wyjście:**  

| Użytkownik | Funkcja | Czas trwania |
| --- | --- | --- |
| user@location.com |Właściwymenu |7 |

**Zapytanie**:

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

**Funkcja LAST** może służyć do pobierania ostatniego zdarzenia w określonym warunku. W tym przykładzie warunek jest zdarzeniem typu Start, partycjonowanie wyszukiwania według **użytkownika i funkcji.** W ten sposób każdy użytkownik i funkcja jest traktowana niezależnie podczas wyszukiwania zdarzenia Start. **LIMIT CZAS TRWANIA** ogranicza wyszukiwanie z powrotem w czasie do 1 godziny między End i Start zdarzeń.

## <a name="detect-the-duration-of-a-condition"></a>Wykrywanie czasu trwania warunku

W przypadku warunków obejmujących wiele zdarzeń funkcja **LGD** może służyć do identyfikowania czasu trwania tego warunku. Załóżmy na przykład, że błąd spowodował, że wszystkie samochody mają nieprawidłową wagę (powyżej 20 000 funtów) i czas trwania tego błędu musi zostać obliczony.

**Wejście**:

| Marka | Time | Waga |
| --- | --- | --- |
| Make1 (Dokonać) |2015-01-01T00:00:01.0000000Z |2000 |
| Make2 (Make2) |2015-01-01T00:00:02.0000000Z |25 000 |
| Make1 (Dokonać) |2015-01-01T00:00:03.0000000Z |26000 |
| Make2 (Make2) |2015-01-01T00:00:04.0000000Z |25 000 |
| Make1 (Dokonać) |2015-01-01T00:00:05.0000000Z |26000 |
| Make2 (Make2) |2015-01-01T00:00:06.0000000Z |25 000 |
| Make1 (Dokonać) |2015-01-01T00:00:07.0000000Z |26000 |
| Make2 (Make2) |2015-01-01T00:00:08.0000000Z |2000 |

**Wyjście:**

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Zapytanie**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
Pierwsza instrukcja **SELECT** koreluje bieżący pomiar masy z poprzednim pomiarem, wyświetlając go razem z bieżącym pomiarem. Drugi **SELECT** spogląda wstecz na ostatnie zdarzenie, w którym *previous_weight* jest mniejsza niż 20000, gdzie bieżąca waga jest mniejsza niż 20000, a *previous_weight* bieżącego wydarzenia była większa niż 20000.

End_fault jest bieżącym zdarzeniem nieukadłym, w którym poprzednie zdarzenie było wadliwe, a Start_fault jest ostatnim zdarzeniem nieukadłym wcześniej.

## <a name="periodically-output-values"></a>Okresowo wartości wyjściowe

W przypadku zdarzeń nieregularnych lub brakujących, regularne dane wyjściowe interwału mogą być generowane z danych bardziej rozrzedzonych. Na przykład wygeneruj zdarzenie co 5 sekund, które zgłasza ostatnio widziany punkt danych.

**Wejście**:

| Time | Wartość |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Wyjście (pierwsze 10 rzędów):**

| Window_end | Last_event. Czas | Last_event. Wartość |
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

**Zapytanie**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Ta kwerenda generuje zdarzenia co 5 sekund i wyprowadza ostatnie zdarzenie, które zostało odebrane wcześniej. Czas trwania **HOPPINGWINDOW** określa, jak daleko wstecz kwerendy wygląda, aby znaleźć najnowsze zdarzenie.

Aby uzyskać więcej informacji, zobacz [Okno Hopping](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="process-events-with-independent-time-substreams"></a>Przetwarzanie zdarzeń z niezależnym czasem (pod strumienie)

Zdarzenia mogą pojawić się późno lub poza kolejnością z powodu pochylenia zegara między producentami zdarzeń, pochylenia zegara między partycjami lub opóźnienia sieci.
Na przykład zegar urządzenia *tollid* 2 znajduje się pięć sekund za *TollID* 1, a zegar urządzenia *tollid* 3 jest dziesięć sekund za *TollID* 1. Obliczenia mogą odbywać się niezależnie dla każdej opłaty drogowej, biorąc pod uwagę tylko własne dane zegara jako sygnaturę czasową.

**Wejście**:

| Tablica licencyjna | Marka | Time | TollID (opłata drogowaID) |
| --- | --- | --- | --- |
| DXE 5291 |Make1 (Dokonać) |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 (Make2) |2015-07-27T00:00:05.0000000Z | 1 |
| QYF 9358 |Make1 (Dokonać) |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 (Make3) |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 (Make2) |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 (Dokonać) |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 (Make3) |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 (Make4) |2015-07-27T00:00:07.0000000Z | 3 |

**Wyjście:**

| TollID (opłata drogowaID) | Liczba |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Zapytanie**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

Klauzula **TIMESTAMP OVER BY** analizuje każdą oś czasu urządzenia niezależnie przy użyciu pod strumieni. Zdarzenie wyjściowe dla każdego *identyfikatora tollid* jest generowane podczas obliczania, co oznacza, że zdarzenia są w porządku w odniesieniu do każdego *identyfikatora TollID,* a nie są zmieniane tak, jakby wszystkie urządzenia były na tym samym zegarze.

Aby uzyskać więcej informacji, zobacz [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="remove-duplicate-events-in-a-window"></a>Usuwanie zduplikowanych zdarzeń w oknie

Podczas wykonywania operacji, takich jak obliczanie średnich zdarzeń w danym oknie czasu, zduplikowane zdarzenia powinny być filtrowane. W poniższym przykładzie drugie zdarzenie jest duplikatem pierwszego.

**Wejście**:  

| DeviceId | Time | Atrybut | Wartość |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:01.0000000Z |Temperatura |50 |
| 2 |2018-07-27T00:00:01.0000000Z |Temperatura |40 |
| 1 |2018-07-27T00:00:05.0000000Z |Temperatura |60 |
| 2 |2018-07-27T00:00:05.0000000Z |Temperatura |50 |
| 1 |2018-07-27T00:00:10.0000000Z |Temperatura |100 |

**Wyjście:**  

| Średnia Wartość | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Zapytanie**:

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

**FUNKCJA LICZ(CZAS ODRĘBNY)** zwraca liczbę odrębnych wartości w kolumnie Czas w przedziale czasu. Dane wyjściowe pierwszego kroku mogą być następnie używane do obliczania średniej dla urządzenia, odrzucając duplikaty.

Aby uzyskać więcej informacji, zobacz [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="session-windows"></a>Sesja systemu Windows

Okno sesji to okno, które rozwija się w miarę występowania zdarzeń i zamyka się do obliczenia, jeśli żadne zdarzenie nie zostanie odebrane po określonym czasie lub jeśli okno osiągnie maksymalny czas trwania.
To okno jest szczególnie przydatne podczas obliczania danych interakcji użytkownika. Okno rozpoczyna się, gdy użytkownik rozpoczyna interakcję z systemem i zamyka się, gdy nie są obserwowane żadne zdarzenia, co oznacza, że użytkownik przestał wchodzić w interakcje.
Na przykład użytkownik wchodzi w interakcję ze stroną sieci web, na której jest rejestrowana liczba kliknięć, okno sesji może służyć do dowiedzieć się, jak długo użytkownik wchodził w interakcję z witryną.

**Wejście**:

| User_id | Time | Adres URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**Wyjście:**

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**Zapytanie**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**Select** projektuje dane istotne dla interakcji użytkownika, wraz z czasem trwania interakcji. Grupowanie danych według użytkownika i **SessionWindow,** który zamyka się, jeśli nie interakcja odbywa się w ciągu 1 minuty, z maksymalnym rozmiarem okna 60 minut.

Aby uzyskać więcej informacji na temat **SessionWindow**, zobacz [Okno sesji](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Rozszerzalność języka za pomocą funkcji zdefiniowanej przez użytkownika w językach JavaScript i C #

Język zapytań usługi Azure Stream Analytics można rozszerzyć o funkcje niestandardowe napisane w języku JavaScript lub C#. Funkcje zdefiniowane przez użytkownika (UDF) to obliczenia niestandardowe/złożone, których nie można łatwo wyrazić przy użyciu języka **SQL.** Te pliki UDF można zdefiniować raz i używać wiele razy w ramach kwerendy. Na przykład UDF może służyć do konwersji szesnastkowej wartości *nvarchar(max)* na wartość *bigint.*

**Wejście**:

| Device_id | HexValue (Wartość hex) |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**Wyjście:**

| Device_id | Wartość dziesiętna |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

Funkcja zdefiniowana przez użytkownika obliczy wartość *bigint* z HexValue na każdym zużytym zdarzeniu.

Aby uzyskać więcej informacji, zobacz [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) i [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Zaawansowane dopasowanie wzoru z MATCH_RECOGNIZE

**MATCH_RECOGNIZE** jest zaawansowanym mechanizmem dopasowywania wzorców, który może służyć do dopasowania sekwencji zdarzeń do dobrze zdefiniowanego wzorca wyrażenia regularnego.
Na przykład bankomat jest monitorowany w czasie rzeczywistym pod kątem awarii, podczas pracy bankomatu, jeśli istnieją dwa kolejne komunikaty ostrzegawcze, które administrator musi otrzymywać powiadomienia.

**Wejście**:

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "Wprowadzanie Pin" | "Sukces" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Otwarcie gniazda pieniędzy" | "Sukces" | 2017-01-26T00:10:07.0000000Z |
| 2 | "Zamknięcie slotu pieniędzy" | "Sukces" | 2017-01-26T00:10:11.0000000Z |
| 1 | "Wprowadzanie ilości wypłaty" | "Sukces" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Otwarcie gniazda pieniędzy" | "Ostrzeżenie" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Drukowanie salda bankowego" | "Ostrzeżenie" | 2017-01-26T00:10:19.0000000Z |

**Wyjście:**

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Otwarcie gniazda pieniędzy" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM intput TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    PARTITON BY ATM_id
    LIMIT DURATION(minute, 1)
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Ta kwerenda pasuje do co najmniej dwóch kolejnych zdarzeń awarii i generuje alarm, gdy spełnione są warunki.
**PATTERN** definiuje wyrażenie regularne, które ma być używane w dopasowaniu, w tym przypadku dowolną liczbę udanych operacji, po których następuje co najmniej dwa kolejne błędy.
Powodzenie i niepowodzenie są definiowane przy użyciu wartości Return_Code, a po spełnieniu warunku **środki** są rzutowane z *ATM_id*, pierwszą operacją ostrzegania i pierwszym czasem ostrzeżenia.

Aby uzyskać więcej informacji, zobacz [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Geofencing i zapytania geoprzestrzenne
Usługa Azure Stream Analytics udostępnia wbudowane funkcje geoprzestrzenne, które mogą służyć do implementowania scenariuszy, takich jak zarządzanie flotą, udostępnianie przejazdów, połączone samochody i śledzenie zasobów.
Dane geoprzestrzenne mogą być pozyskiwania w formatach GeoJSON lub WKT jako część strumienia zdarzeń lub danych referencyjnych.
Na przykład firma specjalizująca się w produkcji maszyn do drukowania paszportów, dzierżawi swoje maszyny rządom i konsulatom. Lokalizacja tych maszyn jest ściśle kontrolowana, aby uniknąć niewłaściwego umieszczenia i ewentualnego wykorzystania do podrabiania paszportów. Każda maszyna jest wyposażona w moduł śledzący GPS, który informacje są przekazywane z powrotem do zadania usługi Azure Stream Analytics.
Producent chciałby śledzić lokalizację tych maszyn i być powiadamiany, jeśli jeden z nich opuści autoryzowany obszar, w ten sposób mogą zdalnie wyłączyć, powiadomić władze i pobrać sprzęt.

**Wejście**:

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**Dane referencyjne:**

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655543462 1 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794)" |

**Wyjście:**

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Zapytanie umożliwia producentowi automatyczne monitorowanie lokalizacji maszyn, uzyskiwanie alertów, gdy maszyna opuszcza dozwoloną geofence. Wbudowana funkcja geoprzestrzenna umożliwia użytkownikom korzystanie z danych GPS w ramach zapytania bez bibliotek innych firm.

Aby uzyskać więcej informacji, zapoznaj się [z geofencing i scenariuszy agregacji geoprzestrzennej z usługi Azure Stream Analytics](geospatial-scenarios.md) artykułu.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
