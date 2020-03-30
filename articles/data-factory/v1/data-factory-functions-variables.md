---
title: Funkcje fabryki danych i zmienne systemowe
description: Zawiera listę funkcji usługi Azure Data Factory i zmiennych systemowych
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73667648"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Fabryka danych platformy Azure — funkcje i zmienne systemowe
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Zmienne systemowe w ustawieniach Data Factory](../control-flow-system-variables.md).

Ten artykuł zawiera informacje o funkcjach i zmiennych obsługiwanych przez usługę Azure Data Factory.

## <a name="data-factory-system-variables"></a>Zmienne systemowe fabryki danych

| Nazwa zmiennej | Opis | Zakres obiektu | Przypadki zakresu json i użycia |
| --- | --- | --- | --- |
| Początek okna |Początek interwału czasu dla bieżącego okna uruchamiania aktywności |aktywność |<ol><li>Określ kwerendy wyboru danych. Zobacz artykuły łącznika, do których odwołuje się artykuł [Działania przenoszenia danych.](data-factory-data-movement-activities.md)</li> |
| OknoWykaz |Koniec interwału czasu dla bieżącego okna uruchamiania aktywności |aktywność |tak samo jak WindowStart. |
| Początek plasterków |Początek interwału czasu dla tworzonego plasterka danych |aktywność<br/>Dataset |<ol><li>Podczas pracy z zestawami danych [Azure Blob](data-factory-azure-blob-connector.md) i [File System określ](data-factory-onprem-file-system-connector.md)dynamiczne ścieżki folderów i nazwy plików.</li><li>Określ zależności wejściowe z funkcjami fabryki danych w kolekcji danych wejściowych aktywności.</li></ol> |
| PlasterekWyciężk |Koniec interwału czasu dla bieżącego plasterka danych. |aktywność<br/>Dataset |tak samo jak SliceStart. |

> [!NOTE]
> Obecnie fabryka danych wymaga, aby harmonogram określony w działaniu dokładnie odpowiada harmonogramowi określonej w dostępności wyjściowego zestawu danych. W związku z tym WindowStart, WindowEnd i SliceStart i SliceEnd zawsze mapują do tego samego okresu czasu i pojedynczego plasterka wyjściowego.
> 

### <a name="example-for-using-a-system-variable"></a>Przykład użycia zmiennej systemowej
W poniższym przykładzie rok, miesiąc, dzień i **godzina SliceStart** są wyodrębniane do oddzielnych zmiennych, które są używane przez **właściwości folderPath** i **fileName.**

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
Funkcje w fabryce danych wraz ze zmiennymi systemowymi można używać do następujących celów:

1. Określanie kwerend wyboru danych (zobacz artykuły łącznika, do których odwołuje się artykuł [Działania przenoszenia danych.](data-factory-data-movement-activities.md)
   
   Składnia wywoływania funkcji fabryki danych to: ** $$ \<funkcja>** dla kwerend wyboru danych i innych właściwości w działaniu i zestawach danych.  
2. Określanie zależności wejściowych z funkcjami fabryki danych w kolekcji danych wejściowych działania.
   
    $$ nie jest potrzebne do określania wyrażeń zależności wejściowych.     

W poniższym przykładzie **właściwość sqlReaderQuery** w pliku JSON jest `Text.Format` przypisywana do wartości zwracaej przez funkcję. W tym przykładzie użyto również zmiennej systemowej o nazwie **WindowStart**, która reprezentuje czas rozpoczęcia okna uruchamiania działania.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Zobacz [temat Niestandardowe ciągi formatów daty i godziny,](https://msdn.microsoft.com/library/8kb3ddd4.aspx) który opisuje różne opcje formatowania, których można użyć (na przykład: ay vs. yyyy). 

### <a name="functions"></a>Funkcje
W poniższych tabelach znajdują się wszystkie funkcje usługi Azure Data Factory:

| Kategoria | Funkcja | Parametry | Opis |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DataPoraz <br/><br/>Y: int |Dodaje godziny Y do podanego czasu X. <br/><br/>Przykład: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: DataPoraz <br/><br/>Y: int |Dodaje minuty Y do X.<br/><br/>Przykład: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: Datetime |Pobiera czas rozpoczęcia dla godziny reprezentowane przez składnik godziny X. <br/><br/>Przykład: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Data |Dodatki (X,Y) |X: DataPoraz<br/><br/>Y: int |Dodaje dni Y do X. <br/><br/>Przykład: 15.09.2013 12:00:00 + 2 dni = 17.09.2013 12:00:00.<br/><br/>Można również odjąć dni, określając Y jako liczbę ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Data |Dodaj miesiące(X,Y) |X: DataPoraz<br/><br/>Y: int |Dodaje Y miesięcy do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Można również odjąć miesiące, określając Y jako liczbę ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Data |Dodaj ćwiartki(X,Y) |X: DataPoraz <br/><br/>Y: int |Dodaje Y * 3 miesięcy do X.<br/><br/>Przykład: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Data |Dodaj tydzień (X,Y) |X: DataPoraz<br/><br/>Y: int |Dodaje Y * 7 dni do X<br/><br/>Przykład: 15.09.2013 12:00:00 + 1 tydzień = 22.09.2013 12:00:00<br/><br/>Można również odjąć tygodnie, określając Y jako liczbę ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Data |Lata add(X,Y) |X: DataPoraz<br/><br/>Y: int |Dodaje Y lat do X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Można również odjąć lata, określając Y jako liczbę ujemną.<br/><br/>Przykład: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Data |Dzień(X) |X: DataPoraz |Pobiera składnik dzień X.<br/><br/>Przykład: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Data |DayOfWeek (X) |X: DataPoraz |Pobiera dzień tygodnia składnik X.<br/><br/>Przykład: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Data |DayOfYear(X) |X: DataPoraz |Pobiera dzień w roku reprezentowane przez składnik roku X.<br/><br/>Przykłady:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Data |DaysInMonth(X) |X: DataPoraz |Pobiera dni w miesiącu reprezentowane przez składnik miesiąca parametru X.<br/><br/>Przykład: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Data |Dzień Zakończenia(X) |X: DataPoraz |Pobiera datę i godzinę, która reprezentuje koniec dnia (składnika dnia) X.<br/><br/>Przykład: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Data |Koniec miesiąca(X) |X: DataPoraz |Pobiera koniec miesiąca reprezentowane przez składnik miesiąca parametru X. <br/><br/>Przykład: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (data i godzina reprezentująca koniec miesiąca września) |
| Data |StartOfDay(X) |X: DataPoraz |Pobiera początek dnia reprezentowane przez składnik dzień parametru X.<br/><br/>Przykład: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Od(X) |X: Ciąg |Analizuj ciąg X do daty. |
| DateTime |Kleszcze(X) |X: DataPoraz |Pobiera ticks właściwości parametru X. Jeden kleszcz jest równy 100 nanosekund. Wartość tej właściwości reprezentuje liczbę znaczników, które upłynęły od północy 12:00:00, 1 stycznia 0001. |
| Tekst |Format(X) |X: Zmienna ciągu |Formatuje tekst (użyj `\\'` kombinacji, aby uciec `'` znak).|

> [!IMPORTANT]
> Podczas korzystania z funkcji w innej funkcji, **$$** nie trzeba używać prefiksu dla funkcji wewnętrznej. Na przykład: $$Text.Format('PartitionKey \\eq\\'my_pkey_filter_value' i RowKey \\ge '{0: yyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). W tym przykładzie **$$** należy zauważyć, że prefiks nie jest używany dla **time.AddHours** funkcji. 

#### <a name="example"></a>Przykład
W poniższym przykładzie parametry wejściowe i wyjściowe dla `Text.Format` działania Hive są określane przy użyciu funkcji i slicestart zmiennej systemowej. 

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

W poniższym przykładzie DateTime parametr dla działania procedury składowanej jest określany przy użyciu text. Formatuj, a zmienną SliceStart. 

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
Aby odczytać dane z poprzedniego dnia, a nie dnia reprezentowanego przez SliceStart, należy użyć funkcji AddDays, jak pokazano w poniższym przykładzie: 

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

Zobacz [temat Niestandardowe ciągi formatów daty i godziny,](https://msdn.microsoft.com/library/8kb3ddd4.aspx) który opisuje różne opcje formatowania, których można użyć (na przykład: yy vs yyyy). 

