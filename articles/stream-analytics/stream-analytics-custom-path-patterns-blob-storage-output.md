---
title: Niestandardowe wzorców ścieżki daty/godziny usługi Azure Stream Analytics obiektu blob magazynu danych wyjściowych (wersja zapoznawcza)
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6945e31825af46628c0945cdf579382b20bdcd68
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414013"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Niestandardowe wzorców ścieżki daty/godziny usługi Azure Stream Analytics obiektu blob magazynu danych wyjściowych (wersja zapoznawcza)

Usługa Azure Stream Analytics obsługuje niestandardowych daty i godziny specyfikatory formatu w ścieżce pliku do magazynu obiektów blob w danych wyjściowych. Zezwalaj na niestandardowe wzorców ścieżki daty/godziny, można określić format danych wyjściowych, była zgodna z konwencjami Hive przesyłania strumieniowego, co daje możliwość wysyłania danych do usługi Azure HDInsight i Azure Databricks dla podrzędnych przetwarzania usługi Azure Stream Analytics. Niestandardowe wzorców ścieżki daty/godziny są łatwo zaimplementować przy użyciu `datetime` — słowo kluczowe w polu Prefiks ścieżki obiektu blob danych wyjściowych, wraz ze specyfikatora formatu. Na przykład `{datetime:yyyy}`.

Użyj tego linku, aby [witryny Azure Portal](https://ms.portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) Aby przełączać flagę funkcji, umożliwiająca niestandardowe wzorców ścieżki daty/godziny dla obiektu blob magazynu danych wyjściowych w wersji zapoznawczej. Ta funkcja zostanie wkrótce włączona w głównej portalu.

## <a name="supported-tokens"></a>Obsługiwane tokeny

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

## <a name="extensibility-and-restrictions"></a>Ograniczenia i rozszerzalność

Można użyć tylu tokenów `{datetime:<specifier>}`, tak jak pokazano w wzorzec ścieżki, aż osiągniesz limit znaków prefiksu ścieżki. Specyfikatory formatu nie można łączyć w ramach jednego tokenu poza kombinacje już według daty i godziny list rozwijanych. 

Dla partycji ścieżki `logs/MM/dd`:

|Prawidłowe wyrażenie   |Nieprawidłowe wyrażenie   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Możesz użyć tego samego specyfikatora formatu wielokrotnie w prefiks ścieżki. Token powtarza się za każdym razem.

## <a name="hive-streaming-conventions"></a>Konwencje przesyłania strumieniowego programu hive

Wzorce niestandardową ścieżkę do magazynu obiektów blob może być używany z Konwencji Hive przesyłania strumieniowego, który oczekuje, że foldery do być oznakowane `column=` w nazwie folderu.

Na przykład `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Niestandardowe dane wyjściowe eliminuje trudności związanych ze skomplikowanymi modyfikowania tabel i ręczne dodanie partycji do portu danych między usługą Azure Stream Analytics i Hive. Zamiast tego liczba folderów mogą być dodawane automatycznie za pomocą:

```
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Przykład

Tworzenie konta magazynu, grupę zasobów, zadanie usługi Stream Analytics i źródła danych wejściowych, zgodnie z opisem w [witryny Azure Portal usługi Azure Stream Analytics](stream-analytics-quick-create-portal.md) przewodnika Szybki Start. Użyj tych samych danych przykładowych, używane w przewodniku Szybki Start, również dostępne na [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Tworzenie ujścia danych wyjściowych obiektu blob przy użyciu następującej konfiguracji:

![Stream Analytics, tworzenie obiektu blob ujścia danych wyjściowych](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Wzorzec pełnej ścieżki jest następująca:

```
year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}
```

Podczas uruchamiania zadania strukturę folderów na podstawie wzorca ścieżki jest tworzony w kontenerze obiektów blob. Możesz przejść do poziomu dnia.

![Stream Analytics obiektu blob danych wyjściowych za pomocą wzorzec ścieżki niestandardowej](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Kolejne kroki

* [Zrozumieć dane wyjściowe z usługi Azure Stream Analytics](stream-analytics-define-outputs.md)
