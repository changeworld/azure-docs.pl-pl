---
title: Analizowanie danych tekstowych w dziennikach w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano różne opcje do analizowania danych dziennika w dokumentacji usługi Azure Monitor, gdy dane są pozyskiwane i gdy jest pobierana w zapytaniu, porównywanie względnych zalet dla każdego.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: ad4839a1b9e951a2bb206518254826a066330000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61426735"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analizowanie danych tekstowych w dziennikach w usłudze Azure Monitor
Niektóre dane dzienników zbieranych przez usługi Azure Monitor będzie zawierać kilka rodzajów informacji w pojedynczej właściwości. Analizowanie tych danych na wiele właściwości atrybutu ułatwić używane w kwerendach. Typowym przykładem jest [dziennik niestandardowy](../../log-analytics/log-analytics-data-sources-custom-logs.md) , umożliwia zbieranie informacji o wpisu dziennika całego z wieloma wartościami w jednej właściwości. Tworząc osobne właściwości dla różnych wartości, można wyszukiwać i agregacji w każdego.

W tym artykule opisano różne opcje do analizowania danych dziennika w usłudze Azure Monitor, gdy dane są pozyskiwane i gdy jest pobierana w zapytaniu, porównywanie względnych zalet dla każdego.


## <a name="parsing-methods"></a>Metody analizy
Można analizować dane, albo w momencie pozyskiwania, gdy dane są zbierane zapytania czasu podczas analizowania danych za pomocą zapytania. Każda strategia ma unikatowe zalety, zgodnie z poniższym opisem.

### <a name="parse-data-at-collection-time"></a>Analizowanie danych w czasie zbierania
Podczas analizy danych w czasie zbierania, skonfiguruj [pól niestandardowych](../../log-analytics/log-analytics-custom-fields.md) , Utwórz nowe właściwości w tabeli. Zapytania nie muszą zawierać wszelka logika analizy i po prostu użyć tych właściwości, jak każdego innego pola w tabeli.

Oto zalety tej metody:

- Łatwiejsze do wykonywania zapytań w zebranych danych, ponieważ nie trzeba uwzględnić przeanalizować poleceń w zapytaniu.
- Lepszą wydajność zapytań, ponieważ zapytanie nie trzeba wykonywać podczas analizowania.
 
Następujące wady tej metody:

- Musi być zdefiniowana wcześniej. Nie może zawierać dane, które zostały już zebrane.
- W przypadku zmiany logiki analizy go będzie dotyczyć tylko nowych danych.
- Mniej opcji analizy niż jest dostępne w zapytaniach.
- Zwiększa czas oczekiwania do zbierania danych.
- Błędy mogą być trudne do obsłużenia.


### <a name="parse-data-at-query-time"></a>Analizowanie danych w czasie wykonywania zapytania
Podczas analizy danych w czasie wykonywania zapytań, możesz uwzględnić logiki w zapytaniu do analizowania danych w wielu polach. Rzeczywiste samej tabeli nie jest modyfikowana.

Oto zalety tej metody:

- Stosuje się do żadnych danych, włącznie z danymi, które już zostały zebrane.
- Zmiany w logice, które mogą natychmiast stosowane do wszystkich danych.
- Elastyczne podczas analizowania opcji w tym wstępnie zdefiniowanych logiki dla struktur danych.
 
Następujące wady tej metody:

- Wymaga bardziej złożone zapytania. To może być ograniczona przez przy użyciu [funkcji, aby zasymulować tabeli](#use-function-to-simulate-a-table).
- Muszą być replikowane przetwarzania logiki w wielu zapytań. Można udostępniać logikę, za pomocą funkcji.
- Można tworzyć obciążenia przy uruchamianiu złożonej logiki rekordu bardzo duże zestawy (miliardów rekordów).

## <a name="parse-data-as-its-collected"></a>Analizowanie danych, zgodnie z ich zebraniu
Zobacz [Tworzenie pól niestandardowych w usłudze Azure Monitor](../platform/custom-fields.md) szczegółowe informacje na temat analizy danych, ponieważ ich zebraniu. Spowoduje to utworzenie właściwości niestandardowe w tabeli, które mogą być używane w zapytaniach, podobnie jak inne właściwości.

## <a name="parse-data-in-query-using-patterns"></a>Analizowanie danych w zapytaniu przy użyciu wzorców
Gdy dane chcesz przeanalizować może zostać zidentyfikowany przez wzorzec powtarzane na rekordy, można użyć różnych operatorów w [język zapytania Kusto](/azure/kusto/query/) można wyodrębnić określone dane w jedną lub więcej nowych właściwości.

### <a name="simple-text-patterns"></a>Wzorce zwykłego tekstu

Użyj [przeanalizować](/azure/kusto/query/parseoperator) — operator w zapytaniu, aby utworzyć jedną lub więcej właściwości niestandardowych, które można wyodrębnić z wyrażenia ciągu. Należy określić wzorzec identyfikację i nazwy właściwości w celu utworzenia. Jest to szczególnie przydatne w przypadku danych z ciągami pary klucz wartość z formularzem, podobnie jak _klucz = wartość_.

Należy wziąć pod uwagę dziennik niestandardowy z danymi w następującym formacie.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Następujące zapytanie będzie przeanalizować te dane do poszczególnych właściwości. Wiersz z _projektu_ jest dodawany do zwrócenia tylko właściwości obliczeniową i nie _RawData_, która jest jedną właściwość zawierający cały wpis z dziennika niestandardowego.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Oto inny przykład, który dzieli nazwę UPN w _AzureActivity_ tabeli.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Wyrażenia regularne
Jeśli Twoje dane mogą zostać zidentyfikowane z wyrażeniem regularnym, można użyć [funkcje, które używają wyrażeń regularnych](/azure/kusto/query/re2) można wyodrębnić poszczególne wartości. W poniższym przykładzie użyto [wyodrębnić](/azure/kusto/query/extractfunction) umożliwiające rozbicie _UPN_ pola z _AzureActivity_ rekordów, a następnie wróć unikatowych użytkowników.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Umożliwia wydajne analizowanie w dużej skali usługi Azure Monitor używa re2 wersji wyrażeń regularnych, który jest podobny, ale nie są identyczne do niektórych innych wariantów wyrażenia regularnego. Zapoznaj się [składni wyrażenia re2](https://aka.ms/kql_re2syntax) Aby uzyskać szczegółowe informacje.


## <a name="parse-delimited-data-in-a-query"></a>Analizowanie danych rozdzielany w zapytaniu
Dane rozdzielane oddziela pola znakiem wspólne, takie jak przecinek w pliku CSV. Użyj [podziału](/azure/kusto/query/splitfunction) funkcję, aby przeanalizować dane rozdzielane przy użyciu ogranicznika, który określisz. Umożliwia to dzięki [rozszerzyć](/azure/kusto/query/extendoperator) operatora, aby zwrócić wszystkie pola danych lub określić poszczególnych pól, które mają zostać uwzględnione w danych wyjściowych.

> [!NOTE]
> Ponieważ podziału zwraca obiekt dynamiczny, wyniki może być konieczne być jawnie rzutowane na typy danych, takich jak ciąg do użycia w operatory i filtry.

Należy wziąć pod uwagę dziennik niestandardowy z danymi w następującym formacie CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Następujące zapytanie może przeanalizować te dane i podsumowanie przez dwa obliczeniowe właściwości. Pierwszy wiersz dzieli _RawData_ właściwość w tablicy ciągów. Każdy z wierszy dalej umożliwia nadanie nazwy poszczególnych właściwości i dodaje je do danych wyjściowych za pomocą funkcji w celu przekonwertowania ich do odpowiedniego typu danych.

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

## <a name="parse-predefined-structures-in-a-query"></a>Analizowanie struktury wstępnie zdefiniowanych w zapytaniu
Jeśli dane są sformatowane w strukturze znane, można użyć jednej z funkcji w [język zapytania Kusto](/azure/kusto/query/) do analizowania struktury wstępnie zdefiniowane:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [Adres URL](/azure/kusto/query/parseurlfunction)
- [Zapytanie adresu URL](/azure/kusto/query/parseurlqueryfunction)
- [Ścieżka pliku](/azure/kusto/query/parsepathfunction)
- [Agent użytkownika](/azure/kusto/query/parse-useragentfunction)
- [Ciąg wersji](/azure/kusto/query/parse-versionfunction)

Poniższe przykładowe zapytanie analizuje _właściwości_ pole _AzureActivity_ tabeli, które są skonstruowane w formacie JSON. Zapisuje wyniki do dynamicznego właściwość o nazwie _parsedProp_, które zawierają indywidualny nazwanej wartości w formacie JSON. Te wartości są używane do filtrowania i podsumowanie wyników zapytania.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Te funkcje analizy może być intensywnie, procesora, więc powinny być używane tylko wtedy, gdy zapytanie używa wielu właściwości z sformatowane dane. W przeciwnym przypadku prostego dopasowywania przetwarzanie wzorca będzie przebiegać szybciej.

Poniższy przykład przedstawia podział kontrolera domeny typu autoryzacji biletu TGT wstępnej. Typ, istnieje tylko w polu EventData, czyli ciąg znaków XML, ale nie inne dane z tego pola jest wymagany. W tym przypadku [przeanalizować](/azure/kusto/query/parseoperator) służy do wybierania wymaganego elementu danych.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Umożliwia symulowanie tabeli — funkcja
Może mieć wielu zapytań, które wykonują samego analizowania określonej tabeli. W tym przypadku [Tworzenie funkcji](functions.md) zwracającego przeanalizowanych danych zamiast replikować analizy logika w każdym zapytaniu. Alias funkcji zamiast oryginalnej tabeli można następnie użyć w innych zapytaniach.

Należy wziąć pod uwagę w powyższym przykładzie rozdzielonych przecinkami dziennika niestandardowego. Aby można było używać przeanalizowane dane w wielu zapytań, tworzenie funkcji przy użyciu następującego zapytania i zapisz go z aliasem _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Możesz teraz użyć tego aliasu _MyCustomCSVLog_ zamiast nazwy tabeli rzeczywiste w zapytaniach, jak pokazano poniżej.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rejestrowania zapytań](log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań.