---
title: Usługa Azure Stream Analytics niestandardowych obiektów blob, partycjonowanie danych wyjściowych
description: W tym artykule opisano niestandardowe wzorców ścieżki daty/godziny i funkcje pola lub atrybuty niestandardowe dla danych wyjściowych z magazynu obiektów blob z zadań usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9cdf99884845a9cb83ac26723c3ea0e7a779ebff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771859"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Usługa Azure Stream Analytics niestandardowych obiektów blob, partycjonowanie danych wyjściowych

Usługa Azure Stream Analytics obsługuje danych wyjściowych obiektu blob niestandardowych partycjonowania z polami niestandardowymi lub atrybutów i niestandardowe daty/godziny wzorców ścieżki. 

## <a name="custom-field-or-attributes"></a>Pole niestandardowe lub atrybutów

Pole niestandardowe lub atrybutów wejściowych poprawić podrzędnych przetwarzania danych i raportowanie przepływów pracy, umożliwiając większą kontrolę nad dane wyjściowe.

### <a name="partition-key-options"></a>Opcje klucza partycji

Klucz partycji lub nazwa kolumny, użyty do partycjonowania danych wejściowych może zawierać znaki alfanumeryczne, łączniki, podkreślenia i spacji. Nie jest możliwe użycie zagnieżdżonych pól jako klucza partycji, chyba że używana w połączeniu z użyciem aliasów.

### <a name="example"></a>Przykład

Załóżmy, że wykonanie zadania trwa danych wejściowych z sesji użytkownika na żywo, połączenie z usługą zewnętrznych gry wideo, w którym pozyskiwanych danych zawiera kolumnę **client_id** do identyfikowania sesji. W celu podzielenia danych przez **client_id**, ustawić pole wzorzec ścieżki obiektu Blob, aby zawierać token partycji **{client_id}** we właściwościach danych wyjściowych obiektu blob podczas tworzenia zadania. Jak w przypadku danych z różnymi **client_id** wartości przepływać przez zadanie usługi Stream Analytics, dane wyjściowe są zapisywane w oddzielnych folderach oparta na pojedynczym **client_id** wartość na folder.

![Wzorzec ścieżki z identyfikatorem klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobnie gdy zadanie wejściowe został danych z czujników z milionów urządzeń, czujników, gdzie czujnik każdego miał **sensor_id**, byłoby wzorzec ścieżki **{sensor_id}** do partycjonowania poszczególnych danych czujnika do różnych folderów.  


Za pomocą interfejsu API REST części danych wyjściowych JSON plik używany dla tego żądania może wyglądać następująco:  

![Dane wyjściowe z interfejsu API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Gdy zadanie zacznie działać, *klientów* kontener może wyglądać podobnie do następującego:  

![Kontener klientów](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Każdy folder może zawierać wiele obiektów blob, w których każdy obiekt blob zawiera jeden lub więcej rekordów. W powyższym przykładzie istnieje pojedynczy obiekt blob w folderze oznaczone jako "06000000" z następującą zawartością:

![Zawartość obiektu blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Należy zauważyć, że każdy rekord w obiekcie blob ma **client_id** kolumny dopasowania folder nazw, ponieważ został kolumn użyty do partycjonowania danych wyjściowych w ścieżce wyjściowej **client_id**.

### <a name="limitations"></a>Ograniczenia

1. Tylko jeden niestandardowego klucza partycji jest dozwolone w właściwości danych wyjściowych obiektu blob wzorzec ścieżki. Wszystkie z następujących wzorców ścieżki są prawidłowe:

   * Klaster1 / {date} / {aFieldInMyData}  
   * Klaster1 / {time} / {aFieldInMyData}  
   * Klaster1 / {aFieldInMyData}  
   * Klaster1 / {date} / {time} / {aFieldInMyData}  

2. Klucze partycji są bez uwzględniania wielkości liter, dlatego klucze partycji, takich jak "John" i "john" są równoważne. Ponadto wyrażenia nie może służyć jako klucze partycji. Na przykład **{columnA + columnB}** nie działa.  

3. Gdy strumień wejściowy składa się z rekordów z kardynalnością klucza partycji w obszarze 8000, rekordy zostaną dołączone do istniejących obiektów blob i tylko tworzyć nowe obiekty BLOB, gdy jest to konieczne. Jeśli kardynalność jest za pośrednictwem 8000 ma żadnej gwarancji, istniejące obiekty BLOB zostaną zapisane i nowe obiekty BLOB nie będzie można utworzyć dowolną liczbę rekordów z tym samym kluczem partycji.  

## <a name="custom-datetime-path-patterns"></a>Niestandardowe wzorców ścieżki daty/godziny

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

## <a name="next-steps"></a>Kolejne kroki

* [Zrozumieć dane wyjściowe z usługi Azure Stream Analytics](stream-analytics-define-outputs.md)
