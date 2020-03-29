---
title: Partycjonowanie danych wyjściowych niestandardowych obiektów blob usługi Azure Stream Analytics
description: W tym artykule opisano niestandardowe wzorce ścieżek DateTime oraz niestandardowe pola lub atrybuty funkcji danych wyjściowych magazynu obiektów blob z zadań usługi Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426383"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Partycjonowanie danych wyjściowych niestandardowych obiektów blob usługi Azure Stream Analytics

Usługa Azure Stream Analytics obsługuje niestandardowe partycjonowanie danych wyjściowych obiektów blob z niestandardowymi polami lub atrybutami oraz niestandardowymi wzorcami ścieżek DateTime. 

## <a name="custom-field-or-attributes"></a>Niestandardowe pole lub atrybuty

Niestandardowe atrybuty pola lub danych wejściowych poprawiają przepływy pracy przetwarzania danych i raportowania niższego rzędu, umożliwiając większą kontrolę nad danymi wyjściowymi.

### <a name="partition-key-options"></a>Opcje klucza partycji

Klucz partycji lub nazwa kolumny używana do partycjonowania danych wejściowych może zawierać znaki alfanumeryczne z myślnikami, podkreśleniami i spacjami. Nie można używać pól zagnieżdżonych jako klucza partycji, chyba że jest używany w połączeniu z aliasami. Kluczem partycji musi być NVARCHAR(MAX).

### <a name="example"></a>Przykład

Załóżmy, że zadanie pobiera dane wejściowe z sesji użytkowników na żywo połączonych z zewnętrzną usługą gier wideo, gdzie pojął dane zawiera kolumnę **client_id** do identyfikacji sesji. Aby podzielić dane według **client_id,** ustaw pole Wzorzec ścieżki obiektu Blob, aby uwzględnić token partycji **{client_id}** we właściwościach wyjściowych obiektu blob podczas tworzenia zadania. Ponieważ dane z różnymi **client_id** wartości przepływają przez zadanie usługi Stream Analytics, dane wyjściowe są zapisywane w oddzielnych folderach na podstawie pojedynczej wartości **client_id** na folder.

![Wzorzec ścieżki z identyfikatorem klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobnie, jeśli dane wejściowe zadania były danymi z czujników z milionów czujników, w których każdy czujnik miał **sensor_id,** wzorzec ścieżki będzie **{sensor_id}** do partycjonowania każdego czujnika danych do różnych folderów.  


Za pomocą interfejsu API REST sekcja wyjściowa pliku JSON używanego dla tego żądania może wyglądać następująco:  

![Dane wyjściowe interfejsu API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Po uruchomieniu zadania kontener *klientów* może wyglądać następująco:  

![Kontener klientów](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Każdy folder może zawierać wiele obiektów blob, w których każdy obiekt blob zawiera jeden lub więcej rekordów. W powyższym przykładzie w folderze oznaczonym jako "06000000" znajduje się pojedynczy obiekt blob z następującą zawartością:

![Zawartość obiektu blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Należy zauważyć, że każdy rekord w obiekcie blob ma **kolumnę client_id** odpowiadającą nazwie folderu, ponieważ kolumna używana do partycjonowania danych wyjściowych w ścieżce wyjściowej została **client_id**.

### <a name="limitations"></a>Ograniczenia

1. Tylko jeden niestandardowy klucz partycji jest dozwolony we właściwości wyjściowej obiektu blob wzorzec ścieżki. Wszystkie następujące wzorce ścieżki są prawidłowe:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Klucze partycji są niewrażliwe na rozmiary, więc klucze partycji, takie jak "John" i "john" są równoważne. Ponadto wyrażenia nie mogą być używane jako klucze partycji. Na przykład **{columnA + columnB}** nie działa.  

3. Gdy strumień wejściowy składa się z rekordów z kardynalność klucz partycji w ramach 8000, rekordy zostaną dołączone do istniejących obiektów blob i tylko utworzyć nowe obiekty blob, gdy jest to konieczne. Jeśli kardynalność jest ponad 8000 nie ma żadnej gwarancji istniejących obiektów blob zostaną zapisane i nowe obiekty blob nie zostaną utworzone dla dowolnej liczby rekordów z tym samym kluczem partycji.

## <a name="custom-datetime-path-patterns"></a>Niestandardowe wzorce ścieżek DateTime

Niestandardowe wzorce ścieżek DateTime umożliwiają określenie formatu wyjściowego, który jest zgodny z konwencjami przesyłania strumieniowego hive, dając usłudze Azure Stream Analytics możliwość wysyłania danych do usługi Azure HDInsight i usługi Azure Databricks w celu dalszego przetwarzania. Niestandardowe wzorce ścieżki DateTime są `datetime` łatwo implementowane przy użyciu słowa kluczowego w polu Prefiks ścieżki danych wyjściowych obiektu blob wraz z specyfikatorem formatu. Na przykład `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Obsługiwane tokeny

Następujące tokeny specyfikatora formatu mogą być używane samodzielnie lub w połączeniu w celu uzyskania niestandardowych formatów DateTime:

|Specyfikator formatu   |Opis   |Wyniki na przykład czas 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Rok jako czterocyfrowy numer|2018|
|{datetime:MM}|Miesiąc od 01 do 12|01|
|{datetime:M}|Miesiąc od 1 do 12|1|
|{datetime:dd}|Dzień od 01 do 31|02|
|{datetime:d}|Dzień od 1 do 12|2|
|{datetime:HH}|Godzina w formacie 24-godzinnym, od 00 do 23|10|
|{datetime:mm}|Minuty od 00 do 24|06|
|{datetime:m}|Minuty od 0 do 24|6|
|{datetime:ss}|Sekundy od 00 do 60|08|

Jeśli nie chcesz używać niestandardowych wzorców DateTime, możesz dodać token {date} i/lub {time} do prefiksu ścieżki, aby wygenerować listy rozwijanej z wbudowanymi formatami DateTime.

![Analiza strumieniowa starych formatów DateTime](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Rozszerzalność i ograniczenia

Można użyć jak najwięcej `{datetime:<specifier>}`tokenów, jak chcesz we wzorcu ścieżki, aż do osiągnięcia limitu znaków prefiksu ścieżki. Specyfikatory formatu nie mogą być łączone w ramach jednego tokenu poza kombinacjami już wymienionymi przez listy rozwijane daty i godziny. 

Dla partycji `logs/MM/dd`ścieżki:

|Prawidłowe wyrażenie   |Nieprawidłowe wyrażenie   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

W prefiksie Ścieżka można używać tego samego specyfikatora formatu wiele razy. Token musi być powtarzany za każdym razem.

### <a name="hive-streaming-conventions"></a>Konwencje przesyłania strumieniowego gałęzi

Niestandardowe wzorce ścieżek dla magazynu obiektów blob mogą być używane z konwencją `column=` przesyłania strumieniowego hive, która oczekuje, że foldery będą oznaczone w nazwie folderu.

Na przykład `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Niestandardowe dane wyjściowe eliminuje kłopotów zmiany tabel i ręczne dodawanie partycji do danych portu między usługi Azure Stream Analytics i hive. Zamiast tego wiele folderów można dodać automatycznie za pomocą:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Przykład

Utwórz konto magazynu, grupę zasobów, zadanie usługi Stream Analytics i źródło danych wejściowych zgodnie z przewodnikiem szybki startowym [usługi Azure Stream Analytics Azure Portal.](stream-analytics-quick-create-portal.md) Użyj tych samych przykładowych danych, które są używane w przewodniku szybki start, dostępnym również w [usłudze GitHub.](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)

Utwórz ujście wyjściowe obiektu blob z następującą konfiguracją:

![Usługa Stream Analytics tworzy ujście wyjściowe obiektów blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Pełny wzorzec ścieżki jest następujący:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Po uruchomieniu zadania struktura folderów na podstawie wzorca ścieżki jest tworzona w kontenerze obiektów blob. Można przejść do szczegółów do poziomu dnia.

![Dane wyjściowe obiektów blob usługi Stream Analytics z niestandardowym wzorcem ścieżki](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Następne kroki

* [Opis produktów z usługi Azure Stream Analytics](stream-analytics-define-outputs.md)
