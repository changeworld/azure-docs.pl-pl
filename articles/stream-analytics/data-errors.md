---
title: Błędy danych dziennika diagnostycznego usługi Azure Stream Analytics
description: W tym artykule opisano różne błędy danych wejściowych i wyjściowych, które mogą wystąpić podczas korzystania z usługi Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0546464b4d1bcc9eaa4fbffe265486985d9c58f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465030"
---
# <a name="azure-stream-analytics-data-errors"></a>Błędy danych usługi Azure Stream Analytics

Błędy danych są błędy, które występują podczas przetwarzania danych.  Te błędy najczęściej występują podczas de-serializacji danych, serializacji i operacji zapisu.  Gdy wystąpią błędy danych, usługa Stream Analytics zapisuje szczegółowe informacje i przykładowe zdarzenia w dziennikach diagnostycznych.  W niektórych przypadkach podsumowanie tych informacji jest również dostarczane za pośrednictwem powiadomień portalu.

W tym artykule opisano różne typy błędów, przyczyny i szczegóły dziennika diagnostycznego dla błędów danych wejściowych i wyjściowych.

## <a name="diagnostic-log-schema"></a>Schemat dziennika diagnostycznego

Zobacz [Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych,](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) aby wyświetlić schemat dzienników diagnostycznych. Następujący JSON jest przykładową wartością dla pola **Właściwości** dziennika diagnostycznego dla błędu danych.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Błędy danych wejściowych

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Przyczyna: Wybrany typ kompresji wejściowej nie jest zgodny z danymi.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wiadomości z wszelkimi błędami deserializacji, w tym nieprawidłowym typem kompresji, są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator komunikatu wejściowego. W przypadku Centrum zdarzeń identyfikatorem jest Identyfikator partycji, Przesunięcie i Numer sekwencyjny.

**Komunikat o błędzie**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Przyczyna: Nagłówek danych wejściowych jest nieprawidłowy. Na przykład csv ma kolumny z duplikatami nazw.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wiadomości z wszelkimi błędami deserializacji, w tym nieprawidłowym nagłówkiem, są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator komunikatu wejściowego. 
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Przyczyna: Kolumny wejściowe zdefiniowane za pomocą funkcji UTWÓRZ TABELĘ lub PRZEZ Sygnatura CZASOWA BY nie istnieją.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: zdarzenia z brakującymi kolumnami są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator komunikatu wejściowego. 
   * Nazwy kolumn, których brakuje. 
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikaty o błędach**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Przyczyna: Nie można przekonwertować danych wejściowych na typ określony w instrukcji CREATE TABLE.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: zdarzenia z błędem konwersji typu są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator komunikatu wejściowego. 
   * Nazwa kolumny i typ oczekiwany.

**Komunikaty o błędach**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Przyczyna: Dane wejściowe nie są w odpowiednim formacie. Na przykład dane wejściowe nie jest prawidłowy JSON.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wszystkie zdarzenia w komunikacie po napotkaniu nieprawidłowego błędu danych są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator komunikatu wejściowego. 
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikaty o błędach**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInPutTimeStamp (Nieprawidłowy sygnatura czasu)

* Przyczyna: Nie można przekonwertować wartości wyrażenia TIMESTAMP BY na datetime.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: zdarzenia z nieprawidłowym sygnaturą czasowej danych wejściowych są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Identyfikator komunikatu wejściowego. 
   * Komunikat o błędzie. 
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInPutTimeStampKey (Klucz) nieprawidłowego znacznika czasu

* Przyczyna: Wartość TIMESTAMP PRZEZ OVER timestampColumn jest NULL.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: zdarzenia z nieprawidłowym wejściowym kluczem sygnatury czasowej są usuwane z danych wejściowych.
* Szczegóły dziennika
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent (Późne wychowywki)

* Przyczyna: Różnica między czasem aplikacji a czasem przybycia jest większa niż okno tolerancji późnego przybycia.
* Powiadomienie portalu pod warunkiem: Nie
* Poziom dziennika diagnostycznego: Informacje
* Zagrożenie: zdarzenia późnego wprowadzania są obsługiwane zgodnie z ustawieniem "Obsłuż inne zdarzenia" w sekcji Kolejność zdarzeń w konfiguracji zadania. Aby uzyskać więcej [informacji,](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)zobacz Zasady obsługi czasu .
* Szczegóły dziennika
   * Czas aplikacji i czas przybycia. 
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent (EarlyInputEvent)

* Przyczyna: Różnica między czasem aplikacji a czasem przybycia jest większa niż 5 minut.
* Powiadomienie portalu pod warunkiem: Nie
* Poziom dziennika diagnostycznego: Informacje
* Zagrożenie: zdarzenia wczesnego wprowadzania są obsługiwane zgodnie z ustawieniem "Obsłuż inne zdarzenia" w sekcji Kolejność zdarzeń w konfiguracji zadania. Aby uzyskać więcej [informacji,](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)zobacz Zasady obsługi czasu .
* Szczegóły dziennika
   * Czas aplikacji i czas przybycia. 
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikat o błędzie**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent (OutOfOrderEvent)

* Przyczyna: Zdarzenie jest uważane za niesapełne zgodnie z zdefiniowanym oknem tolerancji poza kolejnością.
* Powiadomienie portalu pod warunkiem: Nie
* Poziom dziennika diagnostycznego: Informacje
* Zagrożenie: zdarzenia poza kolejnością są obsługiwane zgodnie z ustawieniem "Obsłuż inne zdarzenia" w sekcji Kolejność zdarzeń w konfiguracji zadania. Aby uzyskać więcej [informacji,](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)zobacz Zasady obsługi czasu .
* Szczegóły dziennika
   * Rzeczywista ładowność do kilku kilobajtów.

**Komunikat o błędzie**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Błędy danych wyjściowych

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>Dane wyjścioweKonwersja.

* Przyczyna: Kolumna wymagana dla danych wyjściowych nie istnieje. Na przykład kolumna zdefiniowana jako Azure Table PartitionKey nie istnieje.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wszystkie błędy konwersji danych wyjściowych, w tym brak wymaganej kolumny, są obsługiwane zgodnie z ustawieniem [Zasady danych wyjściowych.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Szczegóły dziennika
   * Nazwa kolumny i identyfikator rekordu lub jego część.

**Komunikat o błędzie**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>Dane wyjścioweDaconversionError.ColumnNameInvalid

* Przyczyna: Wartość kolumny nie jest zgodna z dane wyjściowe. Na przykład nazwa kolumny nie jest prawidłową kolumną tabeli platformy Azure.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wszystkie błędy konwersji danych wyjściowych, w tym nieprawidłowa nazwa kolumny, są obsługiwane zgodnie z ustawieniem [Zasady danych wyjściowych.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Szczegóły dziennika
   * Nazwa kolumny i identyfikatora rekordu lub jego części.

**Komunikat o błędzie**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>Dane wyjścioweKonwersja.TypeConversionError

* Przyczyna: Kolumny nie można przekonwertować na prawidłowy typ w danych wyjściowych. Na przykład wartość kolumny jest niezgodna z ograniczeniami lub typem zdefiniowanym w tabeli SQL.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wszystkie błędy konwersji danych wyjściowych, w tym błąd konwersji typu, są obsługiwane zgodnie z ustawieniem [Zasady danych wyjściowych.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Szczegóły dziennika
   * Nazwa kolumny.
   * Identyfikator rekordu lub część rekordu.

**Komunikat o błędzie**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>Dane wyjścioweWykonwersjaError.RecordExceededSizeLimit

* Przyczyna: Wartość wiadomości jest większa niż rozmiar obsługiwanych danych wyjściowych. Na przykład rekord jest większy niż 1 MB dla danych wyjściowych Usługi Event Hub.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wszystkie błędy konwersji danych wyjściowych, w tym rekordowy limit rozmiaru przekroczenia rozmiaru, są obsługiwane zgodnie z ustawieniem [Zasady danych wyjściowych.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Szczegóły dziennika
   * Identyfikator rekordu lub część rekordu.

**Komunikat o błędzie**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>Plik OutputDataConversionError.DuplicateKey

* Przyczyna: Rekord zawiera już kolumnę o takiej samej nazwie jak kolumna System. Na przykład dane wyjściowe usługi CosmosDB z kolumną o nazwie ID, gdy kolumna identyfikator jest do innej kolumny.
* Powiadomienie portalu pod warunkiem: Tak
* Poziom dziennika diagnostycznego: Ostrzeżenie
* Zagrożenie: wszystkie błędy konwersji danych wyjściowych, w tym klucz duplikatów, są obsługiwane zgodnie z ustawieniem [Zasady danych wyjściowych.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Szczegóły dziennika
   * Nazwa kolumny.
   * Identyfikator rekordu lub część rekordu.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md)

* [Opis monitorowania zadań usługi Stream Analytics i monitorowania zapytań](stream-analytics-monitoring.md)
