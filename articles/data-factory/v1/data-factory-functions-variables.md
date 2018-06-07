---
title: Funkcje fabryki danych i zmienne systemu | Dokumentacja firmy Microsoft
description: Zawiera listę funkcji fabryki danych Azure i zmienne systemu
documentationcenter: ''
author: sharonlo101
manager: craigg
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 265b227520f25ebd1112d940aaf28fed9f88ecdc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621024"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Fabryki danych Azure — funkcje i zmienne systemu
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [zmienne systemowe w fabryce danych w wersji 2](../control-flow-system-variables.md).

Ten artykuł zawiera informacje o funkcjach i zmiennych obsługiwane przez usługi fabryka danych Azure.

## <a name="data-factory-system-variables"></a>Zmienne systemowe fabryki danych
| Nazwa zmiennej | Opis | Zakres obiektu | Zakres JSON i przypadki użycia |
| --- | --- | --- | --- |
| WindowStart |Początek przedziału czasu bieżącego działania Uruchom okno |działanie |<ol><li>Określ kwerend danych zaznaczenia. Zobacz artykuły łącznika, do którego odwołuje się [działań przepływu danych](data-factory-data-movement-activities.md) artykułu.</li> |
| WindowEnd |Koniec przedziału czasu bieżącego działania Uruchom okno |działanie |taka sama jak WindowStart. |
| SliceStart |Początek przedziału czasu dla tworzonym wycinka danych |działanie<br/>Zestaw danych |<ol><li>Określ folder dynamiczne ścieżki i nazwy plików podczas pracy z [obiektów Blob platformy Azure](data-factory-azure-blob-connector.md) i [zestawów danych systemu plików](data-factory-onprem-file-system-connector.md).</li><li>Określ zależności wejściowych z funkcji fabryki danych w kolekcji danych wejściowych działania.</li></ol> |
| SliceEnd |Koniec przedziału czasu dla bieżącego wycinka danych. |działanie<br/>Zestaw danych |taka sama jak SliceStart. |

> [!NOTE]
> Obecnie fabryki danych wymaga, czy określony w działaniu dokładnie harmonogram jest zgodny z harmonogramem określonym w dostępności wyjściowego zestawu danych. W związku z tym WindowStart, WindowEnd i SliceStart i SliceEnd zawsze są mapowane na ten sam okres czasu i wycinek pojedynczym wyjściowym.
> 

### <a name="example-for-using-a-system-variable"></a>Przykład zmiennej systemowej
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

## <a name="data-factory-functions"></a>Funkcje fabryki danych
Możesz użyć funkcji w fabryce danych wraz z zmienne systemowe, do następujących celów:

1. Określanie zapytania wyboru danych (zobacz artykuły łącznika odwołuje się [działań przepływu danych](data-factory-data-movement-activities.md) artykułu.
   
   Składnia służąca do wywołania funkcji fabryki danych jest: **$$ <function>** danych zapytania wyboru i innych właściwości w działaniu i zestawów danych.  
2. Określanie wejściowych zależności przy użyciu funkcji fabryki danych w kolekcji danych wejściowych działania.
   
    $$ nie jest potrzebna do określenia wyrażeń wejściowych zależności.     

W poniższym przykładzie **sqlReaderQuery** właściwość w pliku JSON jest przypisany do wartości zwracanych przez `Text.Format` funkcji. W tym przykładzie użyto również zmiennej systemu o nazwie **WindowStart**, który reprezentuje czas rozpoczęcia okna uruchamiania działania.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Zobacz [niestandardowe ciągi daty i godziny Format](https://msdn.microsoft.com/library/8kb3ddd4.aspx) temat, który opisano różne opcje formatowania, można użyć (na przykład: dni, a rrrr). 

### <a name="functions"></a>Funkcje
W poniższych tabelach przedstawiono wszystkie funkcje w fabryce danych Azure:

| Kategoria | Funkcja | Parametry | Opis |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: Data i godzina <br/><br/>/ Y: int |Dodaje Y godziny do chwili X. <br/><br/>Przykład: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: Data i godzina <br/><br/>/ Y: int |Dodaje minut Y-X.<br/><br/>Przykład: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: Data i godzina |Pobiera godzinę rozpoczęcia, godzinę reprezentowany przez składnik godziny z wartości x. <br/><br/>Przykład: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: Data i godzina<br/><br/>/ Y: int |Dodaje Y dni do X. <br/><br/>Przykład: 9/15/2013 12:00:00 PM + 2 dni = 9/17/2013 12:00:00 PM.<br/><br/>Zbyt odejmowania dni, określając Y jako wartość ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: Data i godzina<br/><br/>/ Y: int |Dodaje Y miesięcy do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Miesięcy można odejmować zbyt określając Y jako wartość ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: Data i godzina <br/><br/>/ Y: int |Dodaje Y * X 3 miesięcy.<br/><br/>Przykład: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: Data i godzina<br/><br/>/ Y: int |Dodaje Y * X 7 dni<br/><br/>Przykład: 9/15/2013 12:00:00 PM + 1 tydzień = 9/22/2013 12:00:00 PM.<br/><br/>Tygodnie można odejmować zbyt określając Y jako wartość ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: Data i godzina<br/><br/>/ Y: int |Dodaje Y lat do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Lat można odejmować zbyt określając Y jako wartość ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X: Data i godzina |Pobiera składnik dni z wartości X.<br/><br/>Przykład: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X: Data i godzina |Pobiera dzień tygodnia składnika wartości X.<br/><br/>Przykład: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: Data i godzina |Pobiera dzień w roku reprezentowany przez składnik roku x.<br/><br/>Przykłady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X: Data i godzina |Pobiera dni w miesiącu reprezentowany przez składnik miesiąca parametr X.<br/><br/>Przykład: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X: Data i godzina |Pobiera daty i godziny zakończenia dnia (składnik dni) x.<br/><br/>Przykład: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X: Data i godzina |Pobiera koniec miesiąca reprezentowany przez składnik miesiąca parametru X. <br/><br/>Przykład: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (Data i godzina zakończenia miesiąca września) |
| Date |StartOfDay(X) |X: Data i godzina |Pobiera początek dnia reprezentowany przez składnik dni z wartości parametru X.<br/><br/>Przykład: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: ciąg |Przeanalizować ciągu X na czas daty. |
| DateTime |Ticks(X) |X: Data i godzina |Pobiera właściwość parametru X to znaczniki osi. Jeden znaczników jest równy 100 nanosekundach. Wartość ta właściwość reprezentuje liczbę znaczników, które upłynęły od północy 12:00:00 1 stycznia 0001. |
| Tekst |Format(X) |X: zmienna string |Formatuje tekst (Użyj `\\'` kombinacja ucieczki `'` znaków).|

> [!IMPORTANT]
> Podczas korzystania z funkcji w innej funkcji, nie trzeba używać **$$** prefiks dla funkcji wewnętrznej. Na przykład: $$Text.Format ("PartitionKey eq \\" my_pkey_filter_value\\"i RowKey ge \\" {0: yyyy-MM-dd gg}\\'', Time.AddHours (SliceStart, -6)). W tym przykładzie należy zauważyć, że **$$** prefiks nie jest używany przez **Time.AddHours** funkcji. 

#### <a name="example"></a>Przykład
W poniższym przykładzie parametrów wejściowych i wyjściowych działania gałęzi są określane za pomocą `Text.Format` funkcji i SliceStart zmiennej systemowej. 

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

W poniższym przykładzie parametr daty i godziny dla działania dotyczącego procedury składowanej jest określany przy użyciu tekstu. Funkcja format, a zmienna SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
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
Można odczytać danych z poprzedniego dnia zamiast reprezentowany przez SliceStart dzień, należy użyć funkcji AddDays, jak pokazano w poniższym przykładzie: 

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

Zobacz [niestandardowe ciągi daty i godziny Format](https://msdn.microsoft.com/library/8kb3ddd4.aspx) temat, który opisano różne opcje formatowania, można użyć (na przykład: RR a rrrr). 

