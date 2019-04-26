---
title: Funkcji usługi fabryka danych i zmiennych systemowych | Dokumentacja firmy Microsoft
description: Zawiera listę funkcji usługi Azure Data Factory i zmienne systemowe
documentationcenter: ''
author: sharonlo101
manager: craigg
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 1d1c9ef5ba355f1944a362bf0e6f5d7ba91a700a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60486519"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Usługa Azure Data Factory — funkcje i zmienne systemowe
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [zmiennych systemowych w usłudze Data Factory](../control-flow-system-variables.md).

Ten artykuł zawiera informacje dotyczące funkcji i zmiennych obsługiwane przez usługę Azure Data Factory.

## <a name="data-factory-system-variables"></a>Zmienne systemowe fabryki danych

| Nazwa zmiennej | Opis | Zakres obiektów | Zakres JSON i przypadki użycia |
| --- | --- | --- | --- |
| WindowStart |Początek przedział czasowy na potrzeby uruchamiania okna bieżącego działania |działanie |<ol><li>Określ zapytaniach wyboru danych. Zobacz artykuły łącznika, do którego odwołuje się [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu.</li> |
| WindowEnd |Koniec przedział czasowy na potrzeby uruchamiania okna bieżącego działania |działanie |taka sama jak WindowStart. |
| SliceStart |Początek przedziału czasu dla wycinka danych, generowany |działanie<br/>Zestaw danych |<ol><li>Określ folder dynamiczne ścieżki i nazwy plików podczas pracy z [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md) i [zestawów danych w systemie plików](data-factory-onprem-file-system-connector.md).</li><li>Określ zależności wejściowe z funkcji usługi fabryka danych w kolekcji danych wejściowych działania.</li></ol> |
| SliceEnd |Koniec przedział czasu dla bieżącego wycinka danych. |działanie<br/>Zestaw danych |taka sama jak parametru SliceStart. |

> [!NOTE]
> Obecnie fabryki danych wymaga, że harmonogramu określonego w działaniu dokładnie jest zgodny z harmonogramem określonym w dostępności zestawu danych wyjściowych. W związku z tym WindowStart, WindowEnd i SliceStart i SliceEnd są zawsze mapowane na tym samym okresie czasu i wycinek pojedynczego wyjścia.
> 

### <a name="example-for-using-a-system-variable"></a>Przykład za pomocą zmiennej systemowej
W poniższym przykładzie, rok, miesiąc, dzień i czas **SliceStart** są wyodrębniane do oddzielnych zmiennych, które są używane przez **folderPath** i **fileName** właściwości.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funkcji usługi fabryka danych
Można użyć funkcji w usłudze data factory wraz ze zmiennych systemowych w następujących celach:

1. Określanie w zapytaniach wyboru danych (zobacz artykuły łącznika odwołuje się [działania przenoszenia danych](data-factory-data-movement-activities.md) artykułu.
   
   Składnia do wywoływania funkcji fabryki danych:  **$$ \<funkcja >** zapytaniach wyboru danych i inne właściwości w działanie i zestawów danych.  
2. Określanie zależności wejściowe przy użyciu funkcji usługi fabryka danych w kolekcji danych wejściowych działania.
   
    $$ nie jest wymagana do określania wyrażenia danych wejściowych zależności.     

W poniższym przykładzie **sqlReaderQuery** właściwości w pliku JSON jest przypisany do wartości zwracanej przez `Text.Format` funkcji. W tym przykładzie użyto również zmienna systemowa o nazwie **WindowStart**, który reprezentuje czas rozpoczęcia okna uruchomienia działania.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Zobacz [Custom Date and Time Format Strings](https://msdn.microsoft.com/library/8kb3ddd4.aspx) temat, który zawiera opis różnych opcji formatowania, można użyć (na przykład: dni, a rrrr). 

### <a name="functions"></a>Funkcje
W poniższej tabeli wymieniono wszystkie funkcje w usłudze Azure Data Factory:

| Category | Funkcja | Parametry | Opis |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Dodaje Y godzin do chwili X. <br/><br/>Przykład: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |Dodaje Y minut x.<br/><br/>Przykład: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: DateTime |Pobiera godzinę rozpoczęcia, godzinę, reprezentowane przez składnik godziny wartości X. <br/><br/>Przykład: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |Dodaje Y dni x. <br/><br/>Przykład: 9/15/2013 12:00:00: 00 + 2 dni = 9/17/2013 12:00:00 PM.<br/><br/>Odejmij dni zbyt, określając Y jako liczba ujemna.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |Dodaje miesięcy Y, x.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Możesz zbyt Odejmij miesięcy, określając Y jako liczba ujemna.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Dodaje Y * X 3 miesięcy.<br/><br/>Przykład: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Dodaje Y * X 7 dni<br/><br/>Przykład: 9/15/2013 12:00:00: 00 + 1 tydzień = 9/22/2013 12:00:00 PM<br/><br/>Tygodnie można odjąć zbyt, określając Y jako liczba ujemna.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |Dodaje lat Y, x.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Lata można odjąć zbyt, określając Y jako liczba ujemna.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X: DateTime |Pobiera składnik dni z wartości X.<br/><br/>Przykład: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X: DateTime |Pobiera składnik dnia tygodnia x.<br/><br/>Przykład: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: DateTime |Pobiera dzień w roku, reprezentowane przez składnik roku wartości X.<br/><br/>Przykłady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X: DateTime |Pobiera dni w miesiącu, reprezentowane przez składnik miesiąca z parametr X.<br/><br/>Przykład: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X: DateTime |Pobiera daty i godziny, reprezentującą koniec dnia (składnik dzień) x.<br/><br/>Przykład: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X: DateTime |Pobiera koniec miesiąca, reprezentowane przez składnik miesiąca z parametr X. <br/><br/>Przykład: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (Data i godzina, reprezentujący koniec miesiąca września) |
| Date |StartOfDay(X) |X: DateTime |Pobiera początek dnia, reprezentowane przez składnik dni z wartości parametru X.<br/><br/>Przykład: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: String |Przeanalizować składni ciągu X czas daty. |
| DateTime |Ticks(X) |X: DateTime |Pobiera znaczniki właściwość parametru X. Jeden znaczników jest równa 100 nanosekund. Wartość tej właściwości reprezentuje liczbę znaczników, które upłynęły od północy 12:00:00, 1 stycznia 0001. |
| Text |Format(X) |X: Zmienna String |Formatuje tekst (Użyj `\\'` kombinacji jako znak ucieczki `'` znaków).|

> [!IMPORTANT]
> Podczas korzystania z funkcji w ramach innej funkcji, nie trzeba używać **$$** prefiks dla wewnętrznej funkcji. Na przykład: $$Text.Format ("PartitionKey eq \\" my_pkey_filter_value\\"i RowKey ge \\" {0: yyyy-MM-dd HH: mm:}\\'', Time.AddHours (SliceStart, -6)). W tym przykładzie należy zauważyć, że **$$** prefiks nie jest używany do **Time.AddHours** funkcji. 

#### <a name="example"></a>Przykład
W poniższym przykładzie parametry wejściowe i wyjściowe dla działania Hive są określane za pomocą `Text.Format` funkcji i zmiennych systemowych SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Przykład 2

W poniższym przykładzie parametr daty/godziny działania dotyczącego procedury składowanej jest określana za pomocą tekstu. Format funkcji i zmiennych SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Przykład 3
Do odczytywania danych z poprzedniego dnia, a nie dzień reprezentowany przez parametru SliceStart, należy użyć funkcji AddDays, jak pokazano w poniższym przykładzie: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Zobacz [Custom Date and Time Format Strings](https://msdn.microsoft.com/library/8kb3ddd4.aspx) temat, który zawiera opis różnych opcji formatowania, można użyć (na przykład: RR a rrrr). 

