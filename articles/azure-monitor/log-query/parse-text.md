---
title: Analizowanie danych tekstowych w dziennikach usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano różne opcje analizowania danych dziennika w rekordach usługi Azure Monitor, gdy dane są pobierane i gdy są pobierane w kwerendzie, porównując względne korzyści dla każdego z nich.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: d7a37d51c411488231205fd036f9a287f5206ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672450"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analizowanie danych tekstowych w dziennikach usługi Azure Monitor
Niektóre dane dziennika zebrane przez usługę Azure Monitor będą zawierać wiele informacji w jednej właściwości. Analizowanie tych danych do wielu właściwości ułatwiają korzystanie w kwerendach. Typowym przykładem jest [dziennik niestandardowy,](../../log-analytics/log-analytics-data-sources-custom-logs.md) który zbiera cały wpis dziennika z wieloma wartościami w jednej właściwości. Tworząc oddzielne właściwości dla różnych wartości, można wyszukiwać i agregować na każdym.

W tym artykule opisano różne opcje analizowania danych dziennika w usłudze Azure Monitor, gdy dane są pobierane i gdy są pobierane w kwerendzie, porównując względne korzyści dla każdego z nich.


## <a name="parsing-methods"></a>Metody analizowania
Dane można analizować w czasie pozyskiwania, gdy dane są zbierane lub w czasie kwerendy podczas analizowania danych za pomocą kwerendy. Każda strategia ma unikalne zalety, jak opisano poniżej.

### <a name="parse-data-at-collection-time"></a>Analizuj dane w czasie zbierania
Podczas analiz y danych w czasie zbierania, można skonfigurować [pola niestandardowe,](../../log-analytics/log-analytics-custom-fields.md) które tworzą nowe właściwości w tabeli. Kwerendy nie muszą zawierać żadnej logiki analizy i po prostu używać tych właściwości, jak każde inne pole w tabeli.

Zalety tej metody są następujące:

- Łatwiej zbadać zebrane dane, ponieważ nie trzeba dołączać poleceń analizy w kwerendzie.
- Lepsza wydajność kwerendy, ponieważ kwerenda nie musi przeprowadzać analizowania.
 
Wady tej metody są następujące:

- Należy zdefiniować z wyprzedzeniem. Nie można uwzględnić danych, które zostały już zebrane.
- Jeśli zmienisz logikę analizowania, będzie dotyczyć tylko nowych danych.
- Mniej opcji analizowania niż dostępne w kwerendach.
- Zwiększa czas opóźnienia do zbierania danych.
- Błędy mogą być trudne do obsługi.


### <a name="parse-data-at-query-time"></a>Analizuj dane w czasie kwerendy
Podczas analizowanie danych w czasie kwerendy, należy uwzględnić logikę w kwerendzie do analizowanie danych w wielu polach. Sama tabela nie jest modyfikowana.

Zalety tej metody są następujące:

- Dotyczy wszystkich danych, w tym danych, które zostały już zebrane.
- Zmiany w logice mogą być stosowane natychmiast do wszystkich danych.
- Elastyczne opcje analizowania, w tym wstępnie zdefiniowana logika dla poszczególnych struktur danych.
 
Wady tej metody są następujące:

- Wymaga bardziej złożonych zapytań. Można to złagodzić za pomocą [funkcji do symulacji tabeli](#use-function-to-simulate-a-table).
- Musi replikować logikę analizowania w wielu kwerendach. Może udostępniać niektóre logiki za pośrednictwem funkcji.
- Można utworzyć obciążenie podczas uruchamiania złożonej logiki względem bardzo dużych zestawów rekordów (miliardy rekordów).

## <a name="parse-data-as-its-collected"></a>Analizuj dane w miarę ich gromadzenia
Zobacz [Tworzenie pól niestandardowych w usłudze Azure Monitor, aby](../platform/custom-fields.md) uzyskać szczegółowe informacje na temat analizowania danych podczas ich zbierania. Spowoduje to utworzenie właściwości niestandardowych w tabeli, które mogą być używane przez kwerendy, podobnie jak inne właściwości.

## <a name="parse-data-in-query-using-patterns"></a>Analizuj dane w kwerendzie przy użyciu wzorców
Gdy dane, które chcesz przeanalizować, mogą być identyfikowane przez wzorzec powtarzany w rekordach, można użyć różnych operatorów w [języku kwerendy Kusto,](/azure/kusto/query/) aby wyodrębnić określony fragment danych na jedną lub więcej nowych właściwości.

### <a name="simple-text-patterns"></a>Proste wzory tekstu

Operator [analizy](/azure/kusto/query/parseoperator) w kwerendzie służy do tworzenia co najmniej jednej właściwości niestandardowych, które można wyodrębnić z wyrażenia ciągu. Należy określić wzorzec do zidentyfikowania i nazwy właściwości do utworzenia. Jest to szczególnie przydatne w przypadku danych z ciągami klucz-wartość o formie podobnej do _key=value_.

Należy wziąć pod uwagę dziennik niestandardowy z danymi w następującym formacie.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Następująca kwerenda będzie przeanalizować te dane do poszczególnych właściwości. Wiersz z _projektem_ jest dodawany tylko do zwracania obliczonych właściwości, a nie _RawData_, która jest pojedynczą właściwością przechowującą cały wpis z dziennika niestandardowego.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Poniżej przedstawiono inny przykład, który rozbija nazwę użytkownika nazwy UPN w tabeli _AzureActivity._

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Wyrażenia regularne
Jeśli dane można zidentyfikować za pomocą wyrażenia regularnego, można użyć [funkcji, które używają wyrażeń regularnych](/azure/kusto/query/re2) do wyodrębniania poszczególnych wartości. W poniższym przykładzie użyto [wyodrębnienia,](/azure/kusto/query/extractfunction) aby wyodrębnić pole _UPN_ z rekordów _usługi AzureActivity,_ a następnie zwrócić różnych użytkowników.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Aby włączyć wydajne analizowanie na dużą skalę, usługa Azure Monitor używa wersji re2 wyrażeń regularnych, która jest podobna, ale nie identyczna z niektórymi innymi wariantami wyrażeń regularnych. Szczegółowe informacje można znaleźć w [składni wyrażenia re2.](https://aka.ms/kql_re2syntax)


## <a name="parse-delimited-data-in-a-query"></a>Analizuj rozdzielane dane w kwerendzie
Rozdzielone dane oddzielają pola wspólnym znakiem, takim jak przecinek w pliku CSV. Funkcja [podziału](/azure/kusto/query/splitfunction) służy do analizowania rozdzielanych danych przy użyciu określonego ogranicznika. Można użyć tego z [operatorem extend,](/azure/kusto/query/extendoperator) aby zwrócić wszystkie pola w danych lub określić poszczególne pola, które mają zostać uwzględnione w danych wyjściowych.

> [!NOTE]
> Ponieważ split zwraca obiekt dynamiczny, wyniki mogą wymagać jawnego rzutowania na typy danych, takie jak ciąg znaków, który ma być używany w operatorach i filtrach.

Należy wziąć pod uwagę dziennik niestandardowy z danymi w następującym formacie CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Następująca kwerenda będzie przeanalizować te dane i podsumować przez dwie właściwości obliczeniowe. Pierwszy wiersz dzieli _RawData_ właściwości do tablicy ciąg. Każdy z następnych wierszy nadaje nazwę poszczególnych właściwości i dodaje je do danych wyjściowych przy użyciu funkcji do konwersji ich do odpowiedniego typu danych.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Analizuj wstępnie zdefiniowane struktury w kwerendzie
Jeśli dane są sformatowane w znanej strukturze, można użyć jednej z funkcji w [języku zapytania Kusto](/azure/kusto/query/) do analizowania wstępnie zdefiniowanych struktur:

- [Json](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [Protokół IPv4](/azure/kusto/query/parse-ipv4function)
- [Adres URL](/azure/kusto/query/parseurlfunction)
- [Zapytanie url](/azure/kusto/query/parseurlqueryfunction)
- [Ścieżka pliku](/azure/kusto/query/parsepathfunction)
- [Agent użytkownika](/azure/kusto/query/parse-useragentfunction)
- [Ciąg wersji](/azure/kusto/query/parse-versionfunction)

Poniższa przykładowa kwerenda analizuje pole _Właściwości_ tabeli _AzureActivity,_ która jest skonstruowana w json. Zapisuje wyniki do właściwości dynamicznej o nazwie _parsedProp_, która zawiera indywidualną wartość o nazwie w JSON. Wartości te są używane do filtrowania i podsumowywania wyników kwerendy.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Te funkcje analizowania mogą być intensywnie obciążające procesor, więc powinny być używane tylko wtedy, gdy kwerenda używa wielu właściwości z sformatowanych danych. W przeciwnym razie proste dopasowanie do wzorca będzie szybsze.

Poniższy przykład przedstawia podział typu preauth kontrolera domeny. Typ istnieje tylko w polu EventData, które jest ciągiem XML, ale nie są potrzebne żadne inne dane z tego pola. W takim przypadku [analizuje](/azure/kusto/query/parseoperator) służy do wyłowić wymagany fragment danych.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Używanie funkcji do symulowania tabeli
Może być wiele zapytań, które wykonują tę samą analizowanie określonej tabeli. W takim przypadku [należy utworzyć funkcję,](functions.md) która zwraca analizowane dane zamiast replikowania logiki analizowania w każdej kwerendzie. Następnie można użyć aliasu funkcji zamiast oryginalnej tabeli w innych kwerendach.

Należy wziąć pod uwagę rozdzielane przecinkami przykład dziennika niestandardowego powyżej. Aby użyć analizowanych danych w wielu kwerendach, utwórz funkcję przy użyciu następującej kwerendy i zapisz ją za pomocą aliasu _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Teraz można użyć aliasu _MyCustomCSVLog_ zamiast rzeczywistej nazwy tabeli w kwerendach, takich jak poniżej.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dziennika](log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań.
