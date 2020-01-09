---
title: Azure Stream Analytics partycjonowanie niestandardowego obiektu BLOB
description: W tym artykule opisano niestandardowe wzorce ścieżki DateTime oraz funkcje niestandardowego pola lub atrybutów dla danych wyjściowych usługi BLOB Storage z zadań Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426383"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics partycjonowanie niestandardowego obiektu BLOB

Azure Stream Analytics obsługuje partycjonowanie niestandardowego obiektu BLOB przy użyciu niestandardowych pól lub atrybutów oraz niestandardowych wzorców ścieżki DateTime. 

## <a name="custom-field-or-attributes"></a>Niestandardowe pole lub atrybuty

Atrybuty pola niestandardowego lub danych wejściowych usprawniają przepływy pracy przetwarzania danych i raportowania przez umożliwienie większej kontroli nad danymi wyjściowymi.

### <a name="partition-key-options"></a>Opcje klucza partycji

Klucz partycji lub nazwa kolumny używana do partycjonowania danych wejściowych mogą zawierać znaki alfanumeryczne z łącznikami, podkreśleniami i spacjami. Nie można używać zagnieżdżonych pól jako klucza partycji, chyba że są używane w połączeniu z aliasami. Klucz partycji musi być NVARCHAR (MAX).

### <a name="example"></a>Przykład

Załóżmy, że zadanie pobiera dane wejściowe z sesji użytkownika na żywo połączone z zewnętrzną usługą gier wideo, w której pozyskiwane dane zawierają kolumnę **client_id** do identyfikowania sesji. Aby podzielić dane przez **client_id**, należy ustawić pole wzorca ścieżki obiektu BLOB w celu uwzględnienia tokenu partycji **{client_id}** we właściwościach danych wyjściowych obiektu BLOB podczas tworzenia zadania. Jako że dane zawierające różne wartości **client_id** przepływają przez zadanie Stream Analytics, dane wyjściowe są zapisywane w oddzielnych folderach na podstawie pojedynczej **client_id** wartości dla każdego folderu.

![Wzorzec ścieżki z identyfikatorem klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobnie, jeśli dane wejściowe zadania były danymi czujnika z milionów czujników, w których każdy czujnik miał **sensor_id**, wzorzec ścieżki będzie **{sensor_id}** do partycjonowania poszczególnych danych czujników w różnych folderach.  


W przypadku korzystania z interfejsu API REST sekcja Output pliku JSON użyta dla tego żądania może wyglądać następująco:  

![Wyjście interfejsu API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Po uruchomieniu zadania kontener *klienci* może wyglądać następująco:  

![Kontener klientów](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Każdy folder może zawierać wiele obiektów blob, w których każdy obiekt BLOB zawiera jeden lub więcej rekordów. W powyższym przykładzie istnieje pojedynczy obiekt BLOB w folderze o nazwie "06000000" z następującą zawartością:

![Zawartość obiektu BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Zwróć uwagę, że każdy rekord w obiekcie blob ma **client_id** kolumnę pasującą do nazwy folderu, ponieważ kolumna używana do partycjonowania danych wyjściowych w ścieżce danych wyjściowych została **client_id**.

### <a name="limitations"></a>Ograniczenia

1. W właściwości Output obiektu BLOB wzorca ścieżki jest dozwolony tylko jeden klucz partycji niestandardowej. Wszystkie następujące wzorce ścieżki są prawidłowe:

   * Klaster1/{Date}/{aFieldInMyData}  
   * Klaster1/{Time}/{aFieldInMyData}  
   * Klaster1/{aFieldInMyData}  
   * Klaster1/{Date}/{Time}/{aFieldInMyData} 
   
2. W kluczach partycji nie jest rozróżniana wielkość liter, dlatego klucze partycji, takie jak "Jan" i "Jan", są równoważne. Wyrażenia nie mogą być również używane jako klucze partycji. Na przykład **{ColumnA + ColumnB}** nie działa.  

3. Gdy strumień wejściowy składa się z rekordów z kardynalnością klucza partycji poniżej 8000, rekordy będą dołączane do istniejących obiektów blob i w razie potrzeby tworzyć nowe obiekty blob. Jeśli Kardynalność przekracza 8000, nie ma gwarancji, że istniejące obiekty blob zostaną zapisane w programie, a nowe obiekty blob nie zostaną utworzone dla dowolnej liczby rekordów z tym samym kluczem partycji.

## <a name="custom-datetime-path-patterns"></a>Niestandardowe wzorce ścieżki DateTime

Zezwalaj na niestandardowe wzorców ścieżki daty/godziny, można określić format danych wyjściowych, była zgodna z konwencjami Hive przesyłania strumieniowego, co daje możliwość wysyłania danych do usługi Azure HDInsight i Azure Databricks dla podrzędnych przetwarzania usługi Azure Stream Analytics. Niestandardowe wzorców ścieżki daty/godziny są łatwo zaimplementować przy użyciu `datetime` — słowo kluczowe w polu Prefiks ścieżki obiektu blob danych wyjściowych, wraz ze specyfikatora formatu. Na przykład `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Obsługiwane tokeny

Następujące generatory kodów specyfikatora formatu można osobno lub razem do osiągnięcia niestandardowych formatów daty/godziny:

|Specyfikator formatu   |Opis   |Wyniki, na przykład czas 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Rok jako liczbę czterocyfrową|2018 r.|
|{datetime:MM}|Miesiąca z zakresu od 01 do 12|01|
|{datetime:M}|Miesiąca z zakresu od 1 do 12|1|
|{datetime:dd}|Dzień z zakresu od 01 do 31|02|
|{datetime:d}|Dzień z zakresu od 1 do 12|2|
|{datetime:HH}|Godzina w formacie 24-godzinnym, z zakresu od 00 do 23|10|
|{datetime:mm}|Minut z zakresu od 00 do 24|06|
|{datetime:m}|Minut z zakresu od 0 do 24|6|
|{datetime:ss}|Sekund z zakresu od 00 do 60|08|

Jeśli nie chcesz użyć niestandardowego wzorców daty/godziny, można dodać {date} lub {time} tokenu do prefiksu ścieżki, aby wygenerować listę rozwijaną przy użyciu wbudowanych formatów daty/godziny.

![Stream Analytics stare formatów daty/godziny](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Ograniczenia i rozszerzalność

Można użyć tylu tokenów `{datetime:<specifier>}`, tak jak pokazano w wzorzec ścieżki, aż osiągniesz limit znaków prefiksu ścieżki. Specyfikatory formatu nie można łączyć w ramach jednego tokenu poza kombinacje już według daty i godziny list rozwijanych. 

Dla partycji ścieżki `logs/MM/dd`:

|Prawidłowe wyrażenie   |Nieprawidłowe wyrażenie   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Możesz użyć tego samego specyfikatora formatu wielokrotnie w prefiks ścieżki. Token powtarza się za każdym razem.

### <a name="hive-streaming-conventions"></a>Konwencje przesyłania strumieniowego programu hive

Wzorce niestandardową ścieżkę do magazynu obiektów blob może być używany z Konwencji Hive przesyłania strumieniowego, który oczekuje, że foldery do być oznakowane `column=` w nazwie folderu.

Na przykład `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Niestandardowe dane wyjściowe eliminuje trudności związanych ze skomplikowanymi modyfikowania tabel i ręczne dodanie partycji do portu danych między usługą Azure Stream Analytics i Hive. Zamiast tego liczba folderów mogą być dodawane automatycznie za pomocą:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Przykład

Tworzenie konta magazynu, grupę zasobów, zadanie usługi Stream Analytics i źródła danych wejściowych, zgodnie z opisem w [witryny Azure Portal usługi Azure Stream Analytics](stream-analytics-quick-create-portal.md) przewodnika Szybki Start. Użyj tych samych danych przykładowych, używane w przewodniku Szybki Start, również dostępne na [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Tworzenie ujścia danych wyjściowych obiektu blob przy użyciu następującej konfiguracji:

![Stream Analytics, tworzenie obiektu blob ujścia danych wyjściowych](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Wzorzec pełnej ścieżki jest następująca:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Podczas uruchamiania zadania strukturę folderów na podstawie wzorca ścieżki jest tworzony w kontenerze obiektów blob. Możesz przejść do poziomu dnia.

![Stream Analytics obiektu blob danych wyjściowych za pomocą wzorzec ścieżki niestandardowej](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Następne kroki

* [Zrozumieć dane wyjściowe z usługi Azure Stream Analytics](stream-analytics-define-outputs.md)
