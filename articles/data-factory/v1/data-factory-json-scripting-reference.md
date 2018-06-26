---
title: Fabryki danych Azure - JSON skryptów odwołania | Dokumentacja firmy Microsoft
description: Udostępnia schematów JSON dla jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d5d085e24efb7008e8275ec51afe00ba18ca9ba0
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753561"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Fabryki danych Azure - JSON skryptów odwołania
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA).


Ten artykuł zawiera przykłady i schematów JSON do definiowania jednostek fabryki danych Azure (potoku, działania, zestawu danych i połączonej usługi).  

## <a name="pipeline"></a>Potok 
Struktura wysokiego poziomu definicji potoku jest następujący: 

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Poniższa tabela zwiera opis właściwości w potoku definicji JSON:

| Właściwość | Opis | Wymagane
-------- | ----------- | --------
| name | Nazwa potoku. Określ nazwę, która reprezentuje akcję że działania lub potoku jest skonfigurowany<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi rozpoczynać się literą, cyfrą lub podkreśleniem (_)</li><li>Następujące znaki nie są dozwolone: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Yes |
| description |Tekst opisujący działania lub potoku do czego służy | Nie |
| activities | Zawiera listę działań. | Yes |
| rozpoczynanie |Data i godzina rozpoczęcia dla potoku. Musi być w [formacie ISO](http://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41. <br/><br/>Użytkownik może określić czas lokalny, na przykład czas EST. Oto przykład: `2016-02-27T06:00:00**-05:00`, która jest szacowana AM 6<br/><br/>Właściwości początkową i końcową razem Określ aktywny okres potoku. Wycinki danych wyjściowych tylko są tworzone z aktywny okres. |Nie<br/><br/>Jeśli określono wartość dla właściwości końca, należy określić wartość dla właściwości rozpoczęcia.<br/><br/>Godziny rozpoczęcia i zakończenia może jednocześnie być puste, aby utworzyć potok. Należy określić zarówno wartości można ustawić okresu aktywności w potoku do uruchamiania. Jeśli nie określono godziny rozpoczęcia i zakończenia podczas tworzenia potoku, można ustawić ich później za pomocą polecenia cmdlet Set-AzureRmDataFactoryPipelineActivePeriod. |
| end |Data czas zakończenia dla potoku. Jeśli zostanie określona, musi być w formacie ISO. Na przykład: 2014-10-14T17:32:41 <br/><br/>Użytkownik może określić czas lokalny, na przykład czas EST. Oto przykład: `2016-02-27T06:00:00**-05:00`, która jest szacowana AM 6<br/><br/>Aby działają przez nieograniczony czas potoku, należy określić 9999-09-09 jako wartość właściwości end. |Nie <br/><br/>Jeśli określono wartość dla właściwości rozpoczęcia, należy określić wartość dla właściwości end.<br/><br/>Zobacz uwagi dla **start** właściwości. |
| isPaused |Jeśli ma wartość true potoku nie działa. Wartość domyślna = false. Ta właściwość umożliwia włączanie lub wyłączanie. |Nie |
| pipelineMode |Metoda planowania działa dla potoku. Dozwolone wartości to: (domyślnie), zaplanowane jednorazowe.<br/><br/>"Zaplanowana" wskazuje, że potoku jest uruchamiane w określonych odstępach czasu zgodnie z jego aktywny okres (czas rozpoczęcia i zakończenia). "Jednorazowe" wskazuje, że potoku jest uruchamiana tylko raz. Potoki jednorazowe utworzonej nie może być zmodyfikowany zaktualizowane obecnie. Zobacz [potoku Onetime](data-factory-create-pipelines.md#onetime-pipeline) szczegółowe informacje o ustawienie jednorazowe. |Nie |
| expirationTime |Czas po utworzeniu, dla którego potoku jest prawidłowy i powinny być zainicjowana. Jeśli nie ma żadnych aktywnych nie powiodło się, lub do czasu działa, potoku zostanie usunięta automatycznie po osiągnięciu czas wygaśnięcia. |Nie |


## <a name="activity"></a>Działanie 
Struktura wysokiego poziomu do działania w potoku definicji (element działań) jest w następujący sposób:

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    }
    "scheduler":
    {
    }
}
```

Następujące tabeli opisano właściwości wewnątrz działania definicji JSON:

| Tag | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa działania. Określ nazwę, która reprezentuje akcję skonfigurowaną działania<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi rozpoczynać się literą, cyfrą lub podkreśleniem (_)</li><li>Następujące znaki nie są dozwolone: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Yes |
| description |Tekst opisujący działanie do czego służy. |Nie |
| type |Określa typ działania. Zobacz [MAGAZYNY danych](#data-stores) i [działań PRZEKSZTAŁCANIA danych](#data-transformation-activities) sekcje dla różnych typów działań. |Yes |
| dane wejściowe |Tabele wejściowe używane na potrzeby działania<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nie dla działania HDInsightStreaming i SqlServerStoredProcedure <br/> <br/> Tak dla wszystkich innych |
| wyjścia |Tabele wyjściowe używany przez działanie.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Yes |
| linkedServiceName |Nazwa połączonej usługi używana na potrzeby działania. <br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. |Tak, aby HDInsight działań, działania usługi Azure Machine Learning i działania dotyczącego procedury składowanej. <br/><br/>Nie dla wszystkich innych |
| typeProperties |Właściwości w sekcji typeProperties są zależne od typu działania. |Nie |
| policy |Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Jeśli nie zostanie określona, używane są zasady domyślne. |Nie |
| Harmonogram |Właściwość "harmonogramu" służy do definiowania żądanego planowania dla działania. Właściwości podrzędnych są takie same jak te w [właściwości availability w zestawie danych](data-factory-create-datasets.md#dataset-availability). |Nie |

### <a name="policies"></a>Zasady
Zasady wpływają na zachowanie czasu wykonania działania, w szczególności, podczas przetwarzania wycinka tabeli. Poniższa tabela zawiera szczegółowe informacje.

| Właściwość | Dozwolone wartości | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| Współbieżność |Liczba całkowita <br/><br/>Wartość maksymalna: 10 |1 |Liczba równoczesnych wykonania działania.<br/><br/>Określa Liczba wykonań działania równoległego, które mogą wystąpić na różnych wycinków. Na przykład jeśli działanie musi przechodzić przez duży zbiór dostępnych danych o większej wartości współbieżności przyspiesza przetwarzania danych. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Określa kolejność wycinki danych, które są przetwarzane.<br/><br/>Na przykład jeśli masz wycinków 2 (w toku co 4 godziny, a innym godzinie 5), a oba oczekują na wykonanie. Jeśli ustawisz executionPriorityOrder jako NewestFirst wycinka godzinie 5 jest przetwarzana najpierw. Podobnie jeśli ustawisz executionPriorityORder jako OldestFIrst wycinka godzinie 4 jest przetwarzany. |
| retry |Liczba całkowita<br/><br/>Maksymalna wartość może być 10 |0 |Liczba ponownych prób przed przetwarzania danych dla wycinka jest oznaczony jako błąd. Do określonego ponownych próba zostanie ponowiona wykonania działania dla wycinka danych. Ponów próbę odbywa się jak najszybciej po awarii. |
| timeout |Zakres czasu |00:00:00 |Limit czasu działania. Przykład: 00:10:00 (oznacza limitu 10 minut)<br/><br/>Jeśli wartość nie została określona lub jest równa 0, limit czasu to nieskończoność.<br/><br/>Jeśli czas przetwarzania danych na wycinek przekracza wartość limitu czasu, anulowaniu, a system podejmuje próbę przetwarzania. Liczba ponownych prób zależy od właściwości ponów próbę. W przypadku przekroczenia limitu czasu stan jest ustawiony na upłynął limit czasu. |
| Opóźnienie |Zakres czasu |00:00:00 |Określ opóźnienie przetwarzania danych powoduje uruchomienie wycinka.<br/><br/>Wykonanie działań dotyczących wycinek danych jest uruchomiona po opóźnienie oczekiwany czas wykonywania.<br/><br/>Przykład: 00:10:00 (oznacza opóźnienia w ciągu 10 minut) |
| Parametr longRetry |Liczba całkowita<br/><br/>Wartość maksymalna: 10 |1 |Liczba długa ponownych prób przed wycinek wykonanie nie powiodło się.<br/><br/>Parametr longRetry prób uzyskają przez longRetryInterval. Dlatego jeśli trzeba określić czas między ponownymi próbami, użyj longRetry. Jeśli został określony zarówno longRetry, jak i ponów próbę, każdej próbie longRetry zawiera ponownych prób i maksymalną liczbę prób ponawiania * longRetry.<br/><br/>Na przykład, jeśli mamy poniższe ustawienia w zasadach działania:<br/>Spróbuj ponownie: 3<br/>Parametr longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Załóżmy istnieje tylko jeden wycinek do wykonania (oczekiwanie stanu) i wykonania działania zawsze kończy się niepowodzeniem. Początkowo może być 3 wykonywanie kolejnych prób. Po każdej próbie stanu wycinka byłoby ponów próbę. Po pierwsze 3 prób się za pośrednictwem stanu wycinka będą LongRetry.<br/><br/>Po upływie godziny (to znaczy wartość longRetryInteval w) będzie inny zestaw 3 wykonywanie kolejnych prób. Po wykonaniu tej nie będzie można stanu wycinka i będą podejmowane próby. Dlatego całkowity 6 podejmowano.<br/><br/>Jeśli wykonanie żadnych zakończy się powodzeniem, stan wycinek jest gotowy, a próby są próby.<br/><br/>Parametr longRetry mogą być używane w sytuacji, w których danych zależnych dociera deterministyczna razy lub ogólnej środowiska jest niestabilnym, w których przetwarzania danych. W takich przypadkach to ponownych prób po kolei może nie pomagają i w ten sposób po interwału czasu powoduje żądanego wyniku.<br/><br/>Word Przestroga: nie należy ustawiać wysokiej wartości longRetry lub longRetryInterval. Zazwyczaj wyższej wartości oznacza innych kwestii systemowych. |
| longRetryInterval |Zakres czasu |00:00:00 |Opóźnienie między próbami czas ponów |

### <a name="typeproperties-section"></a>sekcja typeProperties
Sekcja typeProperties jest różne dla każdego działania. Transformacja działania mają tylko właściwości typu. Zobacz [działań PRZEKSZTAŁCANIA danych](#data-transformation-activities) w tym artykule, aby uzyskać przykłady JSON, które definiują czynności do przekształcenia w potoku. 

**Aktywność kopiowania** ma dwa podpunkty w sekcji typeProperties: **źródła** i **zbiornika**. Zobacz [MAGAZYNY danych](#data-stores) sekcji w niniejszym artykule dla przykłady JSON, które pokazują, jak używać danych przechowywane jako źródła i/lub ujścia. 

### <a name="sample-copy-pipeline"></a>Przykładowy potok kopiowania
W poniższym przykładowym potoku występuje jedno działanie typu **Copy** w sekcji **activities**. W tym przykładzie [aktywności kopiowania](data-factory-data-movement-activities.md) kopiuje dane z magazynu obiektów Blob platformy Azure do bazy danych Azure SQL. 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Pamiętaj o następujących kwestiach:

* W sekcji działań jest tylko jedno działanie, którego parametr **type** (typ) został ustawiony na wartość **Copy**.
* Dane wejściowe dla działania mają ustawienie **InputDataset**, a dane wyjściowe — **OutputDataset**.
* W sekcji **typeProperties** parametr **BlobSource** został określony jako typ źródłowy, a parametr **SqlSink** został określony jako typ ujścia.

Zobacz [MAGAZYNY danych](#data-stores) sekcji w niniejszym artykule dla przykłady JSON, które pokazują, jak używać danych przechowywane jako źródła i/lub ujścia.    

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [samouczek: kopiowanie danych z magazynu obiektów Blob do bazy danych SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

### <a name="sample-transformation-pipeline"></a>Przykładowy potok przekształcania
W poniższym przykładowym potoku występuje jedno działanie typu **HDInsightHive** w sekcji **activities**. W tym przykładzie [działanie HDInsight Hive](data-factory-hive-activity.md) przekształca dane z usługi Azure Blob Storage przez uruchomienie pliku skryptu Hive na klastrze usługi Azure HDInsight Hadoop. 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Pamiętaj o następujących kwestiach: 

* W sekcji działań jest tylko jedno działanie, którego parametr **type** został ustawiony na wartość **HDInsightHive**.
* Plik skryptu programu Hive **partitionweblogs.hql** jest przechowywany na koncie usługi Azure Storage (określonym za pomocą elementu scriptLinkedService o nazwie **AzureStorageLinkedService**) oraz w folderze **script** w kontenerze **adfgetstarted**.
* **Definiuje** sekcji służy do określania ustawienia środowiska uruchomieniowego, które są przekazywane do skryptu hive jako wartości konfiguracji gałąź (np. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Zobacz [działań PRZEKSZTAŁCANIA danych](#data-transformation-activities) w tym artykule, aby uzyskać przykłady JSON, które definiują czynności do przekształcenia w potoku.

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [samouczek: tworzenie swój pierwszy potok do przetwarzania danych przy użyciu klastra usługi Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Połączona usługa
Struktury wysokiego poziomu dla definicji usługi połączonej następująco:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Następujące tabeli opisano właściwości wewnątrz działania definicji JSON:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- | 
| name | Nazwa połączonej usługi. | Yes | 
| właściwości — Typ | Typ połączonej usługi. Na przykład: Usługa Azure Storage, baza danych SQL Azure. |
| typeProperties | Sekcja typeProperties zawiera elementy, które są różne dla każdego magazynu danych lub środowiska obliczeniowe. Zobacz [magazyny danych](#datastores) sekcji dla połączonej usługi przechowywania wszystkich danych i [obliczeniowe środowisk](#compute-environments) dla wszystkich obliczeń połączone usługi |   

## <a name="dataset"></a>Zestaw danych 
Zestaw danych z fabryki danych Azure jest zdefiniowane w następujący sposób:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższym JSON:   

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| name | Nazwa zestawu danych. Zobacz [fabryki danych Azure - reguły nazewnictwa](data-factory-naming-rules.md) dla reguły nazewnictwa. |Yes |Nie dotyczy |
| type | Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługi fabryka danych Azure (na przykład: AzureBlob, AzureSqlTable). Zobacz [MAGAZYNY danych](#data-stores) sekcji dla wszystkich magazynów danych i typy danych obsługiwane przez fabryki danych. | 
| Struktura | Schemat zestawu danych. Zawiera kolumny, jak ich typy, itp. | Nie |Nie dotyczy |
| typeProperties | Właściwości odpowiadający wybranego typu. Zobacz [MAGAZYNY danych](#data-stores) sekcji dla obsługiwanych typów i ich właściwości. |Yes |Nie dotyczy |
| external | Flaga wartości logicznej, aby określić, czy element dataset jawnie jest generowany przez potok fabryki danych, czy nie. |Nie |false |
| availability | Definiuje okna przetwarzania lub skalowania modelu do produkcji zestawu danych. Szczegółowe informacje dotyczące tworzenia wycinków modelu zestawu danych, zobacz [planowania i wykonywania](data-factory-scheduling-and-execution.md) artykułu. |Yes |Nie dotyczy |
| policy |Definiuje kryteria i warunków, które należy spełnić wycinków zestaw danych. <br/><br/>Aby uzyskać więcej informacji, zobacz [zestawie danych zasad](#Policy) sekcji. |Nie |Nie dotyczy |

Każda kolumna **struktury** sekcja zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa kolumny. |Yes |
| type |Typ danych kolumny.  |Nie |
| kultury |.NET na podstawie kultury, który będzie używany podczas typu określono i jest typ architektury .NET `Datetime` lub `Datetimeoffset`. Wartość domyślna to `en-us`. |Nie |
| Format |Ciąg, który będzie używany podczas typu określono i jest typ architektury .NET formatu `Datetime` lub `Datetimeoffset`. |Nie |

W poniższym przykładzie element dataset zawiera trzy kolumny `slicetimestamp`, `projectname`, i `pageviews` i są typu: String, typ String i dziesiętnych odpowiednio.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

W poniższej tabeli opisano właściwości można używać w **dostępności** sekcji:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| frequency |Określa jednostkę czasu dla trybu produkcyjnego wycinek zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: minuty, godziny, dnia, tygodnia, miesiąca |Yes |Nie dotyczy |
| interval |Określa mnożnik częstotliwości<br/><br/>"Interwał częstotliwości x" Określa, jak często jest tworzony wycinek.<br/><br/>Zestaw danych, aby zostać podzielona na godzinę, należy ustawić <b>częstotliwość</b> do <b>godzina</b>, i <b>interwał</b> do <b>1</b>.<br/><br/><b>Uwaga</b>: Jeśli zostanie określona częstotliwość jako minutę, zaleca się ustawić interwał wynoszący nie mniej niż 15 |Yes |Nie dotyczy |
| Styl |Określa, czy wycinek będą tworzone na początku/końca zakresu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Jeśli częstotliwość ma ustawioną wartość miesiąca i styl ma ustawioną wartość EndOfInterval, wycinek jest realizowane ostatniego dnia miesiąca. Jeżeli styl jest ustawiony na StartOfInterval, wycinek jest generowany na pierwszy dzień miesiąca.<br/><br/>Jeśli ustawiono częstotliwość do dnia i styl ma ustawioną wartość EndOfInterval, wycinka jest realizowane w ciągu ostatniej godziny dnia.<br/><br/>Jeśli częstotliwość wynosi godzinę i styl ma ustawioną wartość EndOfInterval, wycinek jest generowany na koniec godziny. Na przykład dla wycinka okres 13: 00 – 14: 00, wycinek jest generowany na 14: 00. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanych przez harmonogram do obliczenia granice wycinek zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli AnchorDateTime ma części daty, które są bardziej szczegółowego niż wartość częstotliwości, bardziej szczegółowego części są ignorowane. <br/><br/>Na przykład jeśli <b>interwał</b> jest <b>co godzinę</b> (częstotliwość: godzinę i interwał: 1) i <b>AnchorDateTime</b> zawiera <b>minut i sekund</b>to <b>minut i sekund</b> części AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| Przesunięcie |Zakres czasu za pomocą której zostaną przesunięte początku i końcu wszystkie fragmenty zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli jest określony zarówno anchorDateTime, jak i przesunięcie wynik jest połączonych shift. |Nie |Nie dotyczy |

W poniższej sekcji dostępności Określa, że wyjściowy zestaw danych jest także co godzinę (albo) danych wejściowych co godzinę zestaw danych jest dostępne:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

**Zasad** sekcja w definicji zestawu danych definiuje kryteria i warunków, które należy spełnić wycinków zestaw danych.

| Nazwa zasad | Opis | Dotyczy | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **obiektów blob platformy Azure** spełnia wymagania minimalny rozmiar (w megabajtach). |Obiekt bob Azure |Nie |Nie dotyczy |
| minimumRows |Sprawdza, czy dane w **bazy danych Azure SQL** lub **tabeli platformy Azure** zawiera minimalną liczbę wierszy. |<ul><li>Azure SQL Database</li><li>Tabela platformy Azure</li></ul> |Nie |Nie dotyczy |

**Przykład:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Jeśli zestaw danych jest tworzonym przez fabryki danych Azure, powinien być oznaczony jako **zewnętrznych**. To ustawienie dotyczy wejść pierwsze działanie w potoku ogólnie rzecz biorąc, chyba że działania lub łańcucha potoku jest używany.

| Name (Nazwa) | Opis | Wymagane | Wartość domyślna |
| --- | --- | --- | --- |
| dataDelay |Czas opóźnienia sprawdzanie dostępności danych zewnętrznych dla danego wycinka. Na przykład jeśli dane są dostępne co godzinę, sprawdź dane zewnętrzne jest dostępna i odpowiednie wycinek jest gotowy można opóźnić przy użyciu dataDelay.<br/><br/>Ma zastosowanie tylko do chwili obecnej.  Na przykład jeśli 1:00 PM od razu i ta wartość to 10 minut, sprawdzanie poprawności rozpoczyna się od 1:22:00.<br/><br/>To ustawienie nie wpływa na wycinków w przeszłości (wycinków z godzina zakończenia wycinek + dataDelay < teraz) są przetwarzane bez opóźnień.<br/><br/>Czasu większą niż 23:59 godziny należy określić przy użyciu `day.hours:minutes:seconds` format. Na przykład aby określić 24 godziny, nie używaj 24:00:00; Zamiast tego należy użyć 1.00:00:00. Jeśli używasz 24:00:00, będzie traktowane jako 24 dni (24.00:00:00). 1 dzień i 4 godziny Określ 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między awarii i następnych ponów próbę. W przypadku niepowodzenia spróbuj następnej próbie po retryInterval. <br/><br/>Jeśli jest 1:00 PM od razu, możemy rozpocząć pierwszej próby. W przypadku czasu oczekiwania na zakończenie pierwszej sprawdzanie poprawności 1 minutę i operacja nie powiodła się, następna ponowna próba wynosi 1:00 1 min (czas trwania) + 1 min (interwału ponawiania prób) = 13:02:00. <br/><br/>Wycinki w przeszłości nie ma żadnego opóźnienia. Ponów próbę odbywa się natychmiast. |Nie |00:01:00 (1 minuta) |
| retryTimeout |Limit czasu dla każdego ponowienia próby.<br/><br/>Jeśli ta właściwość jest ustawiona na 10 minut, sprawdzanie poprawności musi być zakończona w ciągu 10 minut. Jeśli trwa dłużej niż 10 minut, aby wykonać sprawdzanie poprawności, limit czasu próby.<br/><br/>Jeśli upłynie limit czasu wszystkie próby zatwierdzenia, wycinek jest oznaczona jako upłynął limit czasu. |Nie |00:10:00 (10 minut) |
| maximumRetry |Liczba Sprawdź dostępność danych zewnętrznych. Wartość maksymalna dozwolonych jest 10. |Nie |3 |


## <a name="data-stores"></a>MAGAZYNY DANYCH
[Połączona usługa](#linked-service) opisy sekcji określone elementy JSON, które są wspólne dla wszystkich typów połączonych usług. Ta sekcja zawiera szczegółowe informacje o elementach JSON, które są specyficzne dla każdego magazynu danych.

[Dataset](#dataset) opisy sekcji określone elementy JSON, które są wspólne dla wszystkich typów zestawów danych. Ta sekcja zawiera szczegółowe informacje o elementach JSON, które są specyficzne dla każdego magazynu danych.

[Działania](#activity) opisy sekcji określone elementy JSON, które są wspólne dla wszystkich typów działań. Ta sekcja zawiera szczegółowe informacje o elementach JSON, które są specyficzne dla każdego magazynu danych, gdy jest używany jako źródło/ujście w działaniu kopiowania.  

Kliknij łącze do magazynu, który chcesz wyświetlić schematów JSON połączonej usługi, zestawu danych i źródło/ujście dla działania kopiowania.

| Kategoria | Magazyn danych 
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |[Azure Data Lake Store](#azure-datalake-store) |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Azure Table storage](#azure-table-storage) |
| **Bazy danych** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Plik** |[Amazon S3](#amazon-s3) |
| &nbsp; |[System plików](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Inne** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |[Tabela sieci Web](#web-table) |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Połączona usługa
Istnieją dwa typy połączonych usług: połączonej usługi magazynu Azure i połączonej usługi magazynu Azure sygnatury dostępu Współdzielonego.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć konto magazynu Azure do fabryki danych przy użyciu **klucz konta**, Utwórz połączoną usługą magazynu Azure. Aby zdefiniować usługi Azure Storage połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureStorage**. Następnie można określić następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| Parametry połączenia |Podaj informacje wymagane do połączenia z magazynem platformy Azure dla właściwości connectionString. |Yes |

##### <a name="example"></a>Przykład  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS Linked Service
Usługa połączone SAS magazynu Azure umożliwia łączenie konto magazynu Azure do fabryki danych Azure za pomocą udostępnionego podpis dostępu (SAS). Fabryka danych zapewnia ograniczony/czas-powiązane z dostęp do określonego/all zasobów (kontener/obiektów blob) w magazynie. Aby połączyć konto magazynu platformy Azure z fabryki danych przy użyciu sygnatura dostępu współdzielonego, Utwórz SAS magazynu Azure połączonej usługi. Aby zdefiniować SAS magazynu Azure połączonej usługi, ustaw **typu** połączonej usługi, aby **element AzureStorageSas**. Następnie można określić następujące właściwości w **typeProperties** sekcji:   

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| sasUri |Określ udostępniony URI sygnatury dostępu do zasobów usługi Azure Storage, takich jak obiektów blob, kontenera lub tabeli. |Yes |

##### <a name="example"></a>Przykład

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Aby uzyskać więcej informacji na temat następujące połączone usługi, zobacz [łącznika usługi Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych obiektów Blob platformy Azure, ustaw **typu** zestawu danych do **AzureBlob**. Następnie określ następujące właściwości określonych obiektów Blob platformy Azure w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów blob. Przykład: myblobcontainer\myblobfolder\ |Yes |
| fileName |Nazwa obiektu blob. Nazwa pliku jest opcjonalna i z uwzględnieniem wielkości liter.<br/><br/>Jeśli określono nazwę pliku, działania (w tym kopiowania) działa na konkretnego obiektu Blob.<br/><br/>Jeśli nie określono nazwy pliku, kopiowania obejmuje wszystkie obiekty BLOB w ścieżce folderu dla zestawu danych wejściowych.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku będzie poniżej tego formatu: danych. <Guid>.txt (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest opcjonalna właściwość. Można go określić folderPath dynamiczne i nazwę pliku dla danych w serii. czas. Na przykład folderPath mogą nadać parametry dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) artykułu.

### <a name="blobsource-in-copy-activity"></a>BlobSource w przypadku działania kopiowania
Jeśli dane są kopiowane z magazynu obiektów Blob platformy Azure, ustaw **typ źródła** działania kopiowania do **BlobSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. |TRUE, False (wartość domyślna) |Nie |

#### <a name="example-blobsource"></a>Przykład: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink w przypadku działania kopiowania
Jeśli dane są kopiowane do magazynu obiektów Blob Azure, ustaw **typu sink** działania kopiowania do **BlobSink**i określ następujące właściwości w **zbiornika** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania, gdy źródłem jest BlobSource lub systemu plików. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszym poziomem folderu docelowego. Pliki docelowe mają automatycznie wygeneruje nazwę. <br/><br/><b>MergeFiles (domyślnie):</b> scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli zostanie określona nazwa pliku/obiektów Blob, nazwa scalony plik jest określona nazwa; w przeciwnym razie będzie nazwa pliku wygenerowana automatycznie. |Nie |

#### <a name="example-blobsink"></a>Przykład: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Blob](data-factory-azure-blob-connector.md#copy-activity-properties) artykułu. 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Azure Data Lake Store połączonej usługi, Ustaw typ połączonej usługi, aby **AzureDataLakeStore**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **AzureDataLakeStore** | Yes |
| dataLakeStoreUri | Określ informacje o koncie usługi Azure Data Lake Store. Znajduje się w następującym formacie: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Yes |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy usługa Data Lake Store. | Wymagany dla odbiorcy |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy usługa Data Lake Store. | Wymagany dla odbiorcy |
| servicePrincipalId | Określ identyfikator aplikacji klienta. | Tak (dla uwierzytelniania głównej usługi) |
| servicePrincipalKey | Określ klucz aplikacji. | Tak (dla uwierzytelniania głównej usługi) |
| dzierżawa | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Tak (dla uwierzytelniania głównej usługi) |
| Autoryzacji | Kliknij przycisk **autoryzacji** przycisk **Edytor fabryki danych** , a następnie wprowadź Twoje poświadczenia, który przypisuje adres URL automatycznie generowanej autoryzacji do tej właściwości. | Tak (do uwierzytelniania poświadczeń użytkownika)|
| Identyfikator sesji | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. To ustawienie jest generowane automatycznie, gdy używasz Edytor fabryki danych. | Tak (do uwierzytelniania poświadczeń użytkownika) |

#### <a name="example-using-service-principal-authentication"></a>Przykład: przy użyciu uwierzytelniania głównej usługi
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Przykład: przy użyciu uwierzytelniania poświadczeń użytkownika
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawem danych usługi Azure Data Lake Store, ustaw **typu** zestawu danych do **AzureDataLakeStore**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| folderPath |Ścieżka do folderu w usłudze Azure Data Lake i kontenera magazynu. |Yes |
| fileName |Nazwa pliku w magazynie usługi Azure Data Lake. Nazwa pliku jest opcjonalna i z uwzględnieniem wielkości liter. <br/><br/>Określ nazwę pliku, działania (w tym kopiowania) współpracuje w określonym pliku.<br/><br/>Jeśli nie określono nazwy pliku, kopia zawiera wszystkie pliki w ścieżce folderu dla zestawu danych wejściowych.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku będzie poniżej tego formatu: danych. <Guid>.txt (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest opcjonalna właściwość. Można go określić folderPath dynamiczne i nazwę pliku dla danych w serii. czas. Na przykład folderPath mogą nadać parametry dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example"></a>Przykład
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties) artykułu. 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Źródło usługi Azure Data Lake Store w przypadku działania kopiowania
Jeśli dane są kopiowane z usługi Azure Data Lake Store, ustaw **typ źródła** działania kopiowania do **AzureDataLakeStoreSource**i określ następujące właściwości w **źródła**sekcji:

**AzureDataLakeStoreSource** obsługuje następujące właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. |TRUE, False (wartość domyślna) |Nie |

#### <a name="example-azuredatalakestoresource"></a>Przykład: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) artykułu.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Obiekt Sink usługi Azure Data Lake Store w przypadku działania kopiowania
Jeśli dane są kopiowane do usługi Azure Data Lake Store, ustaw **typu sink** działania kopiowania do **AzureDataLakeStoreSink**i określ następujące właściwości w **ujścia** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone automatycznie wygeneruje nazwę.<br/><br/><b>MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli zostanie określona nazwa pliku/obiektów Blob, nazwa scalony plik jest określona nazwa; w przeciwnym razie będzie nazwa pliku wygenerowana automatycznie. |Nie |

#### <a name="example-azuredatalakestoresink"></a>Przykład: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) artykułu. 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować bazy danych Azure rozwiązania Cosmos połączonej usługi, ustaw **typu** połączonej usługi, aby **DocumentDb**i określ następujące właściwości w **typeProperties** sekcji:  

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| Parametry połączenia |Określ informacje potrzebne do łączenia z bazą danych Azure DB rozwiązania Cosmos. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika Azure DB rozwiązania Cosmos](data-factory-azure-documentdb-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować bazy danych Azure rozwiązania Cosmos zestawu danych, ustaw **typu** zestawu danych do **DocumentDbCollection**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| collectionName |Nazwa kolekcji bazy danych Azure rozwiązania Cosmos. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika Azure DB rozwiązania Cosmos](data-factory-azure-documentdb-connector.md#dataset-properties) artykułu.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Źródło kolekcji Azure rozwiązania Cosmos bazy danych w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych Azure rozwiązania Cosmos, ustaw **typ źródła** działania kopiowania do **DocumentDbCollectionSource**i określ następujące właściwości w **źródła**sekcji:


| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ zapytanie można odczytać danych. |Wyślij zapytanie do ciągu obsługuje bazy danych Azure rozwiązania Cosmos. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, która zostanie wykonana instrukcja SQL: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znaki specjalne w celu wskazania, że dokument jest zagnieżdżony |Dowolny znak. <br/><br/>Azure DB rozwiązania Cosmos jest magazynem NoSQL dla dokumentów JSON, których struktury zagnieżdżone są dozwolone. Fabryka danych Azure umożliwia użytkownikowi oznaczenia hierarchii za pomocą nestingSeparator, czyli "." w powyższych przykładach. Z separatorem, działanie kopiowania spowoduje wygenerowanie obiektu "Name" o trzy elementy podrzędne elementy najpierw drugie imię i nazwisko, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Obiekt Sink kolekcji bazy danych Azure rozwiązania Cosmos w przypadku działania kopiowania
Jeśli dane są kopiowane do bazy danych Azure rozwiązania Cosmos, ustaw **typu sink** działania kopiowania do **DocumentDbCollectionSink**i określ następujące właściwości w **zbiornika** sekcji :

| **Właściwość** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Wymagany jest znak specjalny w nazwa kolumny źródłowej, aby wskazać zagnieżdżonych dokumentu. <br/><br/>Na przykład powyżej: `Name.First` w danych wyjściowych tabeli tworzy następującą strukturę JSON w dokumencie rozwiązania Cosmos bazy danych:<br/><br/>"Nazwa": {<br/>    "Pierwszy": "Jan"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropką). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropką). |
| writeBatchSize |Liczba równoległych żądań do usługi Azure DB rozwiązania Cosmos w celu utworzenia dokumentów.<br/><br/>Aby precyzyjnie zdefiniować wydajność podczas kopiowania danych z bazy danych rozwiązania Cosmos Azure przy użyciu tej właściwości. Wraz ze zwiększeniem writeBatchSize, ponieważ więcej żądań równoległych do bazy danych Azure rozwiązania Cosmos są wysyłane, może spodziewać się lepszą wydajność. Jednak należy unikać ograniczania przepustowości, który może zgłaszać komunikat o błędzie: "jest duża szybkość żądania".<br/><br/>Ograniczanie zadecyduje o wiele czynników, w tym rozmiar dokumentów, liczbę dokumentów, indeksowania zasady kolekcji docelowej, itd. Dla operacji kopiowania umożliwiają lepsze kolekcji (na przykład S3) ma największą przepływność dostępne (2500 żądań jednostek na sekundę). |Liczba całkowita |Nie (domyślne: 5) |
| writeBatchTimeout |Poczekaj na ukończenie upłynie limit czasu operacji. |zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika Azure DB rozwiązania Cosmos](data-factory-azure-documentdb-connector.md#copy-activity-properties) artykułu.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować bazy danych SQL Azure połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDatabase**i określ następujące właściwości w **typeProperties** sekcja:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Podaj informacje wymagane do połączenia z wystąpieniem bazy danych SQL Azure dla właściwości connectionString. |Yes |

#### <a name="example"></a>Przykład
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [Łącznik usług Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawem danych usługi Azure SQL Database, ustaw **typu** zestawu danych do **AzureSqlTable**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych SQL Azure, odnoszący się do połączonej usługi. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [Łącznik usług Azure SQL](data-factory-azure-sql-connector.md#dataset-properties) artykułu. 

### <a name="sql-source-in-copy-activity"></a>Źródło SQL w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych SQL Azure, ustaw **typ źródła** działania kopiowania do **SqlSource**i określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji, zobacz [Łącznik usług Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) artykułu. 

### <a name="sql-sink-in-copy-activity"></a>Obiekt Sink SQL w przypadku działania kopiowania
Jeśli dane są kopiowane do bazy danych SQL Azure, ustaw **typu sink** działania kopiowania do **SqlSink**i określ następujące właściwości w **zbiornika** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla aktywności kopiowania wypełnić automatycznie generowane wycinek identyfikator, który służy do oczyszczania danych określonego wycinek czas ponownego uruchomienia. |Nazwa kolumny kolumnę o typie danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej danych upserts (aktualizacje/INSERT) do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli do użycia w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej o tym typie tabeli. Kod procedury składowanej można następnie scalić dane są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [Łącznik usług Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) artykułu. 

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Azure SQL Data Warehouse połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDW**i określ następujące właściwości w **typeProperties** sekcja:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse właściwości connectionString. |Yes |



#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawem danych usługi Azure SQL Data Warehouse, ustaw **typu** zestawu danych do **AzureSqlDWTable**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych Azure SQL Data Warehouse, odnoszący się do połączonej usługi. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) artykułu. 

### <a name="sql-dw-source-in-copy-activity"></a>Źródła magazynu danych SQL w przypadku działania kopiowania
Jeśli dane są kopiowane z usługi Azure SQL Data Warehouse, ustaw **typ źródła** działania kopiowania do **SqlDWSource**i określ następujące właściwości w **źródła** sekcja:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artykułu. 

### <a name="sql-dw-sink-in-copy-activity"></a>Ujścia magazynu danych SQL w przypadku działania kopiowania
Jeśli dane są kopiowane do usługi Azure SQL Data Warehouse, ustaw **typu sink** działania kopiowania do **SqlDWSink**i określ następujące właściwości w **zbiornika** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. |Instrukcja zapytania. |Nie |
| allowPolyBase |Wskazuje, czy do użycia zamiast mechanizmu BULKINSERT PolyBase (jeśli jest to wymagane). <br/><br/> **Przy użyciu programu PolyBase jest zalecanym sposobem ładowanie danych do usługi SQL Data Warehouse.** |True <br/>Wartość FAŁSZ (ustawienie domyślne) |Nie |
| Usługi |Grupy właściwości, które można określić, kiedy **allowPolybase** właściwość jest ustawiona na **true**. |&nbsp; |Nie |
| rejectValue |Określa liczbę lub odsetek wierszy, które można odrzucić przed zapytanie nie powiedzie się. <br/><br/>Dowiedz się więcej o opcjach Odrzuć PolyBase **argumenty** sekcji [Tworzenie tabeli zewnętrznej (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tematu. |0 (domyślnie), 1, 2... |Nie |
| dla właściwości rejectType |Określa, czy opcja rejectValue jest określona jako wartość literału lub wartość procentowa. |Wartość (ustawienie domyślne), wartość procentowa |Nie |
| rejectSampleValue |Określa liczbę wierszy do pobrania przed PolyBase ponownie oblicza procent odrzuconych wierszy. |1, 2, … |Tak, jeśli **dla właściwości rejectType** jest **procent** |
| useTypeDefault |Określa sposób obsługi brakujących wartości w rozdzielane pliki tekstowe, jeśli PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej o tej właściwości z sekcji argumenty w [utworzyć EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Wartość true, False (ustawienie domyślne) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artykułu. 

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługi Azure Search połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSearch**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| url | Adres URL dla usługi Azure Search. | Yes |
| key | Klucz administratora dla usługi Azure Search. | Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Search](data-factory-azure-search-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawem danych usługi Azure Search, ustaw **typu** zestawu danych do **AzureSearchIndex**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| type | Właściwość type musi mieć ustawioną **AzureSearchIndex**.| Yes |
| indexName | Nazwa indeksu usługi Azure Search. Fabryki danych nie powoduje utworzenia indeksu. Indeks musi istnieć w usłudze Azure Search. | Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Search](data-factory-azure-search-connector.md#dataset-properties) artykułu.

### <a name="azure-search-index-sink-in-copy-activity"></a>Obiekt Sink indeksu usługi Azure Search w przypadku działania kopiowania
Jeśli dane są kopiowane do indeksu usługi Azure Search, ustaw **typu sink** działania kopiowania do **AzureSearchIndexSink**i określ następujące właściwości w **ujścia** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Określa, czy należy scalić lub Zastąp, jeśli istnieje już dokument w indeksie. | Merge (ustawienie domyślne)<br/>Upload| Nie |
| WriteBatchSize | Przekazywanie danych do indeksu usługi Azure Search, gdy writeBatchSize osiągnie rozmiar buforu. | 1 do 1000. Wartość domyślna to 1000. | Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Search](data-factory-azure-search-connector.md#copy-activity-properties) artykułu.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Połączona usługa
Istnieją dwa typy połączonych usług: połączonej usługi magazynu Azure i połączonej usługi magazynu Azure sygnatury dostępu Współdzielonego.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć konto magazynu Azure do fabryki danych przy użyciu **klucz konta**, Utwórz połączoną usługą magazynu Azure. Aby zdefiniować usługi Azure Storage połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureStorage**. Następnie można określić następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi mieć ustawioną: **AzureStorage** |Yes |
| Parametry połączenia |Podaj informacje wymagane do połączenia z magazynem platformy Azure dla właściwości connectionString. |Yes |

**Przykład:**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS Linked Service
Usługa połączone SAS magazynu Azure umożliwia łączenie konto magazynu Azure do fabryki danych Azure za pomocą udostępnionego podpis dostępu (SAS). Fabryka danych zapewnia ograniczony/czas-powiązane z dostęp do określonego/all zasobów (kontener/obiektów blob) w magazynie. Aby połączyć konto magazynu platformy Azure z fabryki danych przy użyciu sygnatura dostępu współdzielonego, Utwórz SAS magazynu Azure połączonej usługi. Aby zdefiniować SAS magazynu Azure połączonej usługi, ustaw **typu** połączonej usługi, aby **element AzureStorageSas**. Następnie można określić następujące właściwości w **typeProperties** sekcji:   

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi mieć ustawioną: **element AzureStorageSas** |Yes |
| sasUri |Określ udostępniony URI sygnatury dostępu do zasobów usługi Azure Storage, takich jak obiektów blob, kontenera lub tabeli. |Yes |

**Przykład:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Aby uzyskać więcej informacji na temat następujące połączone usługi, zobacz [łącznika Azure Table Storage](data-factory-azure-table-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw tabel Azure, ustaw **typu** zestawu danych do **AzureTable**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych w tabeli platformy Azure, odnoszący się do połączonej usługi. |Tak. W przypadku tableName bez azureTableSourceQuery wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytania są kopiowane do lokalizacji docelowej. |

#### <a name="example"></a>Przykład

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji na temat następujące połączone usługi, zobacz [łącznika Azure Table Storage](data-factory-azure-table-connector.md#dataset-properties) artykułu. 

### <a name="azure-table-source-in-copy-activity"></a>Źródło tabeli platformy Azure w przypadku działania kopiowania
Jeśli dane są kopiowane z magazynu tabel Azure, ustaw **typ źródła** działania kopiowania do **AzureTableSource**i określ następujące właściwości w **źródła** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania tabeli platformy Azure. Przykłady w następnej sekcji. |Nie. W przypadku tableName bez azureTableSourceQuery wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytania są kopiowane do lokalizacji docelowej. |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy swallow wyjątek tabela nie istnieje. |WARTOŚĆ TRUE<br/>WARTOŚĆ FALSE |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji na temat następujące połączone usługi, zobacz [łącznika Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) artykułu. 

### <a name="azure-table-sink-in-copy-activity"></a>Obiekt Sink tabeli platformy Azure w przypadku działania kopiowania
Jeśli dane są kopiowane do magazynu tabel Azure, ustaw **typu sink** działania kopiowania do **AzureTableSink**i określ następujące właściwości w **zbiornika** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, które mogą być używane przez obiekt sink. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, których wartości są używane jako klucze partycji. Jeśli nie zostanie określony, AzureTableDefaultPartitionKeyValue jest używana jako klucza partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, których wartości kolumn używanych jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb do wstawiania danych do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy wartości zastąpienia lub scalić zostać istniejących wierszy w tabeli wyników ze zgodnymi kluczami partycji i wiersza. <br/><br/>Aby dowiedzieć się więcej na temat działania tych ustawień (scalania i Zastąp), zobacz [wstawienia lub scalania jednostki](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [wstawienia lub Zastąp jednostki](https://msdn.microsoft.com/library/azure/hh452242.aspx) tematów. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli, a żadna z tych opcji usuwa wiersze w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. |merge (ustawienie domyślne)<br/>Zamień |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy zostaje trafiony writeBatchSize lub writeBatchTimeout. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, gdy zostaje trafiony writeBatchSize lub writeBatchTimeout |zakres czasu<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (domyślnie magazynu klienta domyślny limit czasu wartość 90 s) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji na temat następujące połączone usługi, zobacz [łącznika Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) artykułu. 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Redshift Amazon połączonej usługi, ustaw **typu** połączonej usługi, aby **AmazonRedshift**i określ następujące właściwości w **typeProperties** sekcji :  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |IP adres lub nazwę hosta serwera Amazon Redshift. |Yes |
| port |Numer portu TCP używany przez serwer Amazon Redshift do nasłuchiwania dla połączeń klienta. |Nie, wartość domyślna: 5439 |
| baza danych |Nazwa bazy danych Amazon Redshift. |Yes |
| nazwa użytkownika |Nazwa użytkownika, który ma dostęp do bazy danych. |Yes |
| hasło |Hasło dla konta użytkownika. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik Amazon Redshift](#data-factory-amazon-redshift-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować Amazon Redshift zestawu danych, ustaw **typu** zestawu danych do **RelationalTable**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Amazon Redshift, odnoszący się do połączonej usługi. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) |


#### <a name="example"></a>Przykład

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznik Amazon Redshift](#data-factory-amazon-redshift-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania 
Jeśli dane są kopiowane z Amazon Redshift, ustaw **typ źródła** działania kopiowania do **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (Jeśli **tableName** z **dataset** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznik Amazon Redshift](#data-factory-amazon-redshift-connector.md#copy-activity-properties) artykułu.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować IBM DB2 połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesDB2**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera bazy danych DB2. |Yes |
| baza danych |Nazwa bazy danych DB2. |Yes |
| Schemat |Nazwa schematu w bazie danych. Nazwa schematu jest rozróżniana wielkość liter. |Nie |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych DB2. Możliwe wartości to: anonimowe, podstawowe i systemu Windows. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych DB2. |Yes |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznik IBM DB2](#data-factory-onprem-db2-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować bazy danych DB2 zestawu danych, ustaw **typu** zestawu danych do **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych DB2, odnoszący się do połączonej usługi. Właściwość tableName jest rozróżniana wielkość liter. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) 

#### <a name="example"></a>Przykład
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik IBM DB2](#data-factory-onprem-db2-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z IBM DB2, ustaw **typ źródła** działania kopiowania do **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `"query": "select * from "MySchema"."MyTable""`. |Nie (Jeśli **tableName** z **dataset** jest określona) |

#### <a name="example"></a>Przykład
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznik IBM DB2](#data-factory-onprem-db2-connector.md#copy-activity-properties) artykułu.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować MySQL połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesMySql**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera MySQL. |Yes |
| baza danych |Nazwa bazy danych MySQL. |Yes |
| Schemat |Nazwa schematu w bazie danych. |Nie |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych MySQL. Możliwe wartości to: `Basic`. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika do połączenia z bazą danych MySQL. |Yes |
| hasło |Określ hasło dla określonego konta użytkownika. |Yes |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych MySQL. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych MySQL, ustaw **typu** zestawu danych do **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych MySQL, odnoszący się do połączonej usługi. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika MySQL](data-factory-onprem-mysql-connector.md#dataset-properties) artykułu. 

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych MySQL, ustaw **typ źródła** działania kopiowania do **RelationalSource**i określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (Jeśli **tableName** z **dataset** jest określona) |


#### <a name="example"></a>Przykład
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties) artykułu. 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Oracle połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesOracle**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| driverType | Określ sterowniku można skopiować danych z/do bazy danych programu Oracle. Dozwolone wartości to **Microsoft** lub **ODP** (ustawienie domyślne). Zobacz [obsługiwanych wersji i instalacji](#supported-versions-and-installation) sekcji Szczegóły sterownika. | Nie |
| Parametry połączenia | Podaj informacje wymagane do połączenia z wystąpieniem bazy danych programu Oracle dla właściwości connectionString. | Yes |
| gatewayName | Nazwa bramy, czy jest używany do łączenia się z serwerem Oracle lokalnej |Yes |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Oracle, ustaw **typu** zestawu danych do **OracleTable**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych programu Oracle, odnoszący się do połączonej usługi. |Nie (Jeśli **oracleReaderQuery** z **OracleSource** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) artykułu.

### <a name="oracle-source-in-copy-activity"></a>Oracle źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych Oracle, ustaw **typ źródła** działania kopiowania do **OracleSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| oracleReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable` <br/><br/>Jeśli nie zostanie określony, która zostanie wykonana instrukcja SQL: `select * from MyTable` |Nie (Jeśli **tableName** z **dataset** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artykułu.

### <a name="oracle-sink-in-copy-activity"></a>Obiekt Sink Oracle w przypadku działania kopiowania
Jeśli dane są kopiowane am bazą danych Oracle, ustaw **typu sink** działania kopiowania do **OracleSink**i określ następujące właściwości w **zbiornika** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |zakres czasu<br/><br/> Przykład: 00:30:00 (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 100) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla aktywności kopiowania wypełnić automatycznie generowane wycinek identyfikator, który służy do oczyszczania danych określonego wycinek czas ponownego uruchomienia. |Nazwa kolumny kolumnę o typie danych binary(32). |Nie |

#### <a name="example"></a>Przykład
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artykułu.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować PostgreSQL połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesPostgreSql**i określ następujące właściwości w **typeProperties** sekcja:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera PostgreSQL. |Yes |
| baza danych |Nazwa bazy danych PostgreSQL. |Yes |
| Schemat |Nazwa schematu w bazie danych. Nazwa schematu jest rozróżniana wielkość liter. |Nie |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych PostgreSQL. Możliwe wartości to: anonimowe, podstawowe i systemu Windows. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych PostgreSQL. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznik PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych PostgreSQL, ustaw **typu** zestawu danych do **RelationalTable**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych PostgreSQL, odnoszący się do połączonej usługi. Właściwość tableName jest rozróżniana wielkość liter. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) |

#### <a name="example"></a>Przykład
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznik PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych programu PostgreSQL, ustaw **typ źródła** działania kopiowania do **RelationalSource**i określ następujące właściwości w **źródła** sekcja:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: "zapytania": "Wybierz * z \"MySchema\".\" MyTable\"". |Nie (Jeśli **tableName** z **dataset** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artykułu.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować SAP Business magazynu (BW) połączonej usługi, ustaw **typu** połączonej usługi, aby **SapBw**i określ następujące właściwości w **typeProperties** sekcji :  

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie programu SAP BW. | ciąg | Yes
systemNumber | Numer systemu systemu SAP BW. | Liczba dziesiętna dwucyfrowe reprezentowany jako ciąg. | Yes
clientId | Identyfikator klienta w systemie SAP W klienta. | Trzycyfrowa liczba dziesiętna reprezentowany jako ciąg. | Yes
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Yes
hasło | Hasło dla użytkownika. | ciąg | Yes
gatewayName | Nazwa bramy, która powinna być używana do nawiązania połączenia lokalnego wystąpienia programu SAP BW usługi fabryka danych. | ciąg | Yes
encryptedCredential | Ciąg zaszyfrowane poświadczenia. | ciąg | Nie

#### <a name="example"></a>Przykład

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować programu SAP BW zestawu danych, ustaw **typu** zestawu danych do **RelationalTable**. Nie ma żadnych właściwości specyficzne dla typu obsługiwane dla zestawu danych SAP BW typu **RelationalTable**.  

#### <a name="example"></a>Przykład

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties) artykułu. 

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z SAP Business Warehouse, ustaw **typ źródła** działania kopiowania do **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcja:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie MDX, które można odczytać danych z wystąpienia programu SAP BW. | Zapytania MDX. | Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) artykułu. 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować SAP HANA połączonej usługi, ustaw **typu** połączonej usługi, aby **SapHana**i określ następujące właściwości w **typeProperties** sekcji:  

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się z wystąpieniem SAP HANA. Jeśli serwer używa portu dostosowane, określ `server:port`. | ciąg | Yes
Typ authenticationType | Typ uwierzytelniania. | Ciąg. "Basic" lub "Windows" | Yes 
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Yes
hasło | Hasło dla użytkownika. | ciąg | Yes
gatewayName | Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązywania połączenia z lokalnym wystąpieniem SAP HANA. | ciąg | Yes
encryptedCredential | Ciąg zaszyfrowane poświadczenia. | ciąg | Nie

#### <a name="example"></a>Przykład

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Aby uzyskać więcej informacji, zobacz [łącznika SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties) artykułu.
 
### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych SAP HANA, ustaw **typu** zestawu danych do **RelationalTable**. Nie ma żadnych właściwości specyficzne dla typu obsługiwane dla zestawu danych SAP HANA typu **RelationalTable**. 

#### <a name="example"></a>Przykład

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika SAP HANA](data-factory-sap-hana-connector.md#dataset-properties) artykułu. 

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z magazynu danych SAP HANA, ustaw **typ źródła** działania kopiowania do **RelationalSource**i określ następujące właściwości w **źródła** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie SQL do odczytywania danych z wystąpieniem SAP HANA. | Zapytanie SQL. | Yes |


#### <a name="example"></a>Przykład


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties) artykułu.


## <a name="sql-server"></a>Oprogramowanie SQL Server

### <a name="linked-service"></a>Połączona usługa
Tworzenie połączonej usługi typu **OnPremisesSqlServer** do połączenia z lokalną bazą danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis specyficzne dla lokalnej usługi SQL Server połączone elementy JSON.

Poniższa tabela zawiera opis specyficzne dla usługi SQL Server połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Powinien mieć ustawioną właściwość type: **OnPremisesSqlServer**. |Yes |
| Parametry połączenia |Określ connectionString informacje wymagane do połączenia z lokalną bazą danych programu SQL Server, przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Yes |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych programu SQL Server. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **domainname\\username**. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |

Można szyfrować poświadczeń przy użyciu **AzureRmDataFactoryEncryptValue nowy** polecenia cmdlet i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (**EncryptedCredential** właściwość):  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: JSON dla przy użyciu uwierzytelniania programu SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: JSON dla przy użyciu uwierzytelniania systemu Windows

Jeśli podano nazwę użytkownika i hasło, bramy służą one do personifikacja określonego konta użytkownika nawiązać połączenia z lokalną bazą danych programu SQL Server. W przeciwnym razie brama łączy się z serwerem SQL, bezpośrednio z kontekstem zabezpieczeń bramy (jego konta uruchamiania).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych programu SQL Server, ustaw **typu** zestawu danych do **SqlServerTable**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych serwera SQL, odnoszący się do połączonej usługi. |Yes |

#### <a name="example"></a>Przykład
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu SQL Server](data-factory-sqlserver-connector.md#dataset-properties) artykułu. 

### <a name="sql-source-in-copy-activity"></a>Źródło SQL w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych programu SQL Server, należy ustawić **typ źródła** działania kopiowania do **SqlSource**i określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. Może odwoływać się wiele tabel z bazy danych odwołuje się zestaw danych wejściowych. Jeśli nie zostanie określony, która zostanie wykonana instrukcja SQL: Wybierz z MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury przechowywanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono dla SqlSource, odbywa się działanie kopii tego zapytania względem źródła danych programu SQL Server można pobrać danych.

Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia zapytania select w celu uruchomienia bazy danych programu SQL Server. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.

> [!NOTE]
> Jeśli używasz **sqlReaderStoredProcedureName**, należy określić wartość dla **tableName** właściwość w zestawie danych JSON. Nie ma żadnych operacji sprawdzania poprawności, jednak wykonywać na tej tabeli.


#### <a name="example"></a>Przykład
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

W tym przykładzie **sqlReaderQuery** dla SqlSource został określony. Działanie kopiowania uruchamia to zapytanie względem źródła danych programu SQL Server można pobrać danych. Można również określić procedury składowanej, podając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry). SqlReaderQuery można odwoływać się wiele tabel w bazie danych odwołuje się zestaw danych wejściowych. Nie jest ograniczona do tabeli, ustawić jako typeProperty tableName zestawu danych.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia zapytania select w celu uruchomienia bazy danych programu SQL Server. Jeśli definicji zestawu danych nie ma on struktury, wszystkie kolumny są wybierane w tabeli.

Aby uzyskać więcej informacji, zobacz [łącznika programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artykułu. 

### <a name="sql-sink-in-copy-activity"></a>Obiekt Sink SQL w przypadku działania kopiowania
Jeśli dane są kopiowane do bazy danych programu SQL Server, należy ustawić **typu sink** działania kopiowania do **SqlSink**i określ następujące właściwości w **zbiornika** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie zanim upłynie limit czasu operacji wstawiania wsadowego oczekiwania. |zakres czasu<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że dane określonych wycinek jest wyczyszczone. Aby uzyskać więcej informacji, zobacz [powtarzalności](#repeatability-during-copy) sekcji. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla aktywności kopiowania wypełnić automatycznie generowane wycinek identyfikator, który służy do oczyszczania danych określonego wycinek czas ponownego uruchomienia. Aby uzyskać więcej informacji, zobacz [powtarzalności](#repeatability-during-copy) sekcji. |Nazwa kolumny kolumnę o typie danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej danych upserts (aktualizacje/INSERT) do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry dla procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametry procedury składowanej. |Nie |
| sqlWriterTableType |Należy określić nazwę typu tabeli do użycia w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej o tym typie tabeli. Kod procedury składowanej można następnie scalić dane są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="example"></a>Przykład
Potok zawiera działanie kopiowania, który jest skonfigurowany do używania tych zestawów danych wejściowych i wyjściowych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **BlobSource** i **zbiornika** ustawiono typ **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artykułu. 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Sybase połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesSybase**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera programu Sybase. |Yes |
| baza danych |Nazwa bazy danych programu Sybase. |Yes |
| Schemat |Nazwa schematu w bazie danych. |Nie |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych programu Sybase. Możliwe wartości to: anonimowe, podstawowe i systemu Windows. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych programu Sybase. |Yes |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych programu Sybase, ustaw **typu** zestawu danych do **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych programu Sybase, odnoszący się do połączonej usługi. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu Sybase](data-factory-onprem-sybase-connector.md#dataset-properties) artykułu. 

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych programu Sybase, ustaw **typ źródła** działania kopiowania do **RelationalSource**i określ następujące właściwości w **źródła** sekcji :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (Jeśli **tableName** z **dataset** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties) artykułu.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Teradata połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesTeradata**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Nazwa serwera programu Teradata. |Yes |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych programu Teradata. Możliwe wartości to: anonimowe, podstawowe i systemu Windows. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych programu Teradata. |Yes |

#### <a name="example"></a>Przykład
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych obiektów Teradata Blob, ustaw **typu** zestawu danych do **RelationalTable**. Obecnie nie ma żadnych właściwości typu, obsługiwane dla zestawu danych programu Teradata. 

#### <a name="example"></a>Przykład
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu Teradata](data-factory-onprem-teradata-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych programu Teradata, ustaw **typ źródła** działania kopiowania do **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties) artykułu.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Cassandra połączone usługi, ustaw **typu** połączonej usługi, aby **OnPremisesCassandra**, a następnie określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| host |Jeden lub więcej adresów IP lub nazw hostów serwerów Cassandra.<br/><br/>Określ rozdzielaną przecinkami listę adresów IP lub nazw hostów, aby nawiązać połączenie wszystkie serwery jednocześnie. |Yes |
| port |Port TCP używany przez serwer Cassandra nasłuchiwanie dla połączeń klienta. |Nie, wartość domyślna: 9042 |
| Typ authenticationType |Podstawowa lub anonimowe |Yes |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli authenticationType ustawiany jest podstawowy. |
| hasło |Określ hasło dla konta użytkownika. |Tak, jeśli authenticationType ustawiany jest podstawowy. |
| gatewayName |Nazwa bramy, która służy do łączenia z bazą danych Cassandra lokalnymi. |Yes |
| encryptedCredential |Poświadczenie szyfrowane przez bramę. |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować Cassandra zestawu danych, ustaw **typu** zestawu danych do **CassandraTable**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| przestrzeni kluczy |Nazwa schematu bazy danych Cassandra lub przestrzeni kluczy. |Tak (Jeśli **zapytania** dla **CassandraSource** nie jest zdefiniowana). |
| tableName |Nazwa tabeli w bazie danych Cassandra. |Tak (Jeśli **zapytania** dla **CassandraSource** nie jest zdefiniowana). |

#### <a name="example"></a>Przykład

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties) artykułu. 

### <a name="cassandra-source-in-copy-activity"></a>Źródło Cassandra w przypadku działania kopiowania
Jeśli dane są kopiowane z Cassandra, ustaw **typ źródła** działania kopiowania do **CassandraSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Zapytania SQL 92 lub CQL zapytania. Zobacz [odwołania CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Korzystając z zapytania SQL, określ **przestrzeni kluczy name.table nazwy** do reprezentowania tabeli ma dotyczyć zapytanie. |Nie (jeśli są zdefiniowane tableName oraz przestrzeni kluczy w zestawie danych). |
| consistencyLevel |Poziom spójności Określa, jak wiele replik musi odpowiedzieć na żądanie odczytu przed zwróceniem danych do aplikacji klienckiej. Cassandra sprawdza określonej liczby replik danych do spełnienia żądania odczytu. |JEDNĄ, DWIE, TRZY, KWORUM, WSZYSTKIE, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Zobacz [Konfigurowanie spójność danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) szczegółowe informacje. |Nie. Domyślna wartość to jeden. |

#### <a name="example"></a>Przykład
  
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties) artykułu.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować bazy danych MongoDB połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesMongoDB**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| serwer |Adres IP lub hosta nazwę serwera bazy danych MongoDB. |Yes |
| port |Port TCP używany przez serwer bazy danych MongoDB do nasłuchiwania dla połączeń klienta. |Opcjonalne, wartość domyślna: 27017 |
| Typ authenticationType |Podstawowy, lub anonimowe. |Yes |
| nazwa użytkownika |Konto użytkownika do bazy danych MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| hasło |Hasło dla użytkownika. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, który ma być używany w celu sprawdzenia poświadczeń dla uwierzytelniania. |Opcjonalnie (jeśli jest używane uwierzytelnianie podstawowe). domyślne: używa konta administratora i baza danych określona za pomocą właściwości databaseName. |
| databaseName |Nazwa bazy danych MongoDB, które chcesz uzyskać dostęp. |Yes |
| gatewayName |Nazwa bramy, która uzyskuje dostęp do magazynu danych. |Yes |
| encryptedCredential |Poświadczenie szyfrowane przez bramę. |Optional (Opcjonalność) |

#### <a name="example"></a>Przykład

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [artykułu łącznika bazy danych MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych MongoDB, ustaw **typu** zestawu danych do **MongoDbCollection**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| collectionName |Nazwa kolekcji w bazie danych MongoDB. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Aby uzyskać więcej informacji, zobacz [artykułu łącznika bazy danych MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Źródłowej bazy danych MongoDB w przypadku działania kopiowania
Jeśli dane są kopiowane z bazy danych MongoDB, ustaw **typ źródła** działania kopiowania do **MongoDbSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL 92. Na przykład: `select * from MyTable`. |Nie (Jeśli **collectionName** z **dataset** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [artykułu łącznika bazy danych MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Amazon S3 połączonej usługi, ustaw **typu** połączonej usługi, aby **AwsAccessKey**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| accessKeyID |Identyfikator klucza tajnego dostępu. |ciąg |Yes |
| secretAccessKey |Samego klucza tajnego dostępu. |Zaszyfrowanego ciągu tajny |Yes |

#### <a name="example"></a>Przykład
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [Amazon S3 artykułu łącznika](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować Amazon S3 zestawu danych, ustaw **typu** zestawu danych do **AmazonS3**, a następnie określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| bucketName |Nazwa pakietu S3. |Ciąg |Yes |
| key |Klucz obiektu S3. |Ciąg |Nie |
| Prefiks |Prefiks klucza obiektu S3. Wybrano obiektów, w której klucze uruchomienia z tym prefiksem. Ma zastosowanie tylko wtedy, gdy klucz jest pusty. |Ciąg |Nie |
| wersja |Wersja obiektu S3, jeśli włączono S3 przechowywania wersji. |Ciąg |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie | |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie | |


> [!NOTE]
> bucketName + klawisz Określa lokalizację, w którym zasobnika jest nadrzędny kontener dla obiektów S3 i klucz jest pełną ścieżką do obiektu S3 obiektu S3.

#### <a name="example-sample-dataset-with-prefix"></a>Przykład: Przykładowego zestawu danych z prefiksem

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Przykład: Zestaw danych przykładowych (wersją)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Przykład: Dynamiczne ścieżki S3
W przykładzie używamy stałej wartości dla właściwości klucza i bucketName w zestawie danych Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Program może obliczyć klucza i bucketName dynamicznie w czasie wykonywania za pomocą zmiennych systemowych, takich jak SliceStart fabryki danych.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Możesz to zrobić takie same właściwości prefiks Amazon S3 zestawu danych. Zobacz [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md) listę obsługiwanych funkcjach i zmiennych.

Aby uzyskać więcej informacji, zobacz [Amazon S3 artykułu łącznika](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w przypadku działania kopiowania
Jeśli dane są kopiowane z Amazon S3, ustaw **typ źródła** działania kopiowania do **FileSystemSource**, a następnie określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Określa, czy do rekursywnie lista S3 obiektów w katalogu. |wartość true, false |Nie |


#### <a name="example"></a>Przykład


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [Amazon S3 artykułu łącznika](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>System plików


### <a name="linked-service"></a>Połączona usługa
System plików lokalnych można połączyć z fabryki danych Azure z **na lokalnym serwerze plików** połączonej usługi. Poniższa tabela zawiera opisy elementy JSON, które są specyficzne dla usługi z lokalnym serwerem plików połączonych.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Upewnij się, że właściwość type ma ustawioną **OnPremisesFileServer**. |Yes |
| host |Określa ścieżkę katalogu głównego folderu, który chcesz skopiować. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady. |Yes |
| Nazwa użytkownika |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (Jeśli wybierzesz encryptedCredential) |
| hasło |Określ hasło dla użytkownika (nazwa użytkownika). |Nie (Jeśli wybierzesz encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzureRmDataFactoryEncryptValue. |Nie (Jeśli wybierzesz określić identyfikator użytkownika i hasło w postaci zwykłego tekstu) |
| gatewayName |Nazwa bramy, która fabryka danych ma używać do łączenia z serwerem plików lokalnych. |Yes |

#### <a name="sample-folder-path-definitions"></a>Ścieżka folderu definicje 
| Scenariusz | Host w definicji usługi połączonej | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na komputerze bramy zarządzania danymi: <br/><br/>Przykłady: D:\\ \* lub D:\folder\subfolder\\* |D:\\ \\ (dla danych zarządzania bramy 2.0 i nowsze wersje) <br/><br/> localhost (dla starszych niż 2.0 bramy zarządzania danych) |. \\ \\ lub folderu\\\\podfolder (dla danych zarządzania bramy 2.0 i nowsze wersje) <br/><br/>D:\\ \\ lub D:\\\\folderu\\\\podfolder (dla wersji bramy poniżej 2.0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\MójSerwer\\udostępnianie\\ \* lub \\ \\MójSerwer\\udostępnianie\\folderu\\podfolderu\\* |\\\\\\\\MójSerwer\\\\udziału |. \\ \\ lub folderu\\\\podfolderu |


#### <a name="example-using-username-and-password-in-plain-text"></a>Przykład: Przy użyciu nazwy użytkownika i hasła w postaci zwykłego tekstu

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Przykład: Użycie encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [artykułu łącznika systemu plików](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować System plików zestawu danych, ustaw **typu** zestawu danych do **FileShare**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Określa podrzędną do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** do folderu ścieżki oparte na wycinku rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku jest w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| obiektu fileFilter |Określ filtr służący do wybierania podzbioru pliki w ścieżce folderu, a nie wszystkie pliki. <br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: "obiektu"fileFilter: "* .log"<br/>Przykład 2: "obiektu"fileFilter: 2016 - 1-?. txt"<br/><br/>Należy pamiętać, że tego obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych z udziału plików. |Nie |
| partitionedBy |PartitionedBy służy do określenia dynamiczne folderPath/fileName danych szeregu czasowego. Przykładem jest folderPath sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**; i są obsługiwane poziomy: **optymalna** i **najszybszym**. zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można użyć nazwy pliku i obiektu fileFilter jednocześnie.

#### <a name="example"></a>Przykład

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [artykułu łącznika systemu plików](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w przypadku działania kopiowania
Jeśli dane są kopiowane z systemu plików, należy ustawić **typ źródła** działania kopiowania do **FileSystemSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z podfoldery lub tylko określonego folderu. |Wartość true, False (ustawienie domyślne) |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Aby uzyskać więcej informacji, zobacz [artykułu łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>W przypadku działania kopiowania obiektu Sink systemu plików
Jeśli dane są kopiowane do systemu plików, należy ustawić **typu sink** działania kopiowania do **FileSystemSink**i określ następujące właściwości w **zbiornika** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania, gdy źródłem jest BlobSource lub systemu plików. |**PreserveHierarchy:** zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/>**FlattenHierarchy:** wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone z automatycznie generowaną nazwą.<br/><br/>**MergeFiles:** scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli określono nazwę pliku nazwy/obiektów blob, nazwa scalony plik jest określona nazwa. W przeciwnym razie jest nazwa pliku wygenerowana automatycznie. |Nie |
Auto-

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [artykułu łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować FTP połączonej usługi, ustaw **typu** połączonej usługi, aby **SerwerFTP**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| host |Nazwa lub adres IP serwera FTP |Yes |&nbsp; |
| Typ authenticationType |Określanie typu uwierzytelniania |Yes |Basic anonimowe |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera FTP |Nie |&nbsp; |
| hasło |Hasło dla użytkownika (username) |Nie |&nbsp; |
| encryptedCredential |Zaszyfrowane poświadczenia dostępu do serwera FTP |Nie |&nbsp; |
| gatewayName |Nazwa bramy zarządzania danymi do nawiązywania połączenia z serwerem FTP lokalnej |Nie |&nbsp; |
| port |Port, na którym nasłuchuje serwer FTP |Nie |21 |
| enableSsl |Określ, czy za pomocą FTP za pośrednictwem kanału SSL/TLS |Nie |true |
| enableServerCertificateValidation |Określ, czy włączyć weryfikacji certyfikatu serwera SSL przy użyciu FTP za pośrednictwem kanału SSL/TLS |Nie |true |

#### <a name="example-using-anonymous-authentication"></a>Przykład: Przy użyciu uwierzytelniania anonimowego

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Przykład: Przy użyciu nazwy użytkownika i hasła w postaci zwykłego tekstu dla uwierzytelniania podstawowego

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Przykład: Przy użyciu portu, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Przykład: Użycie encryptedCredential uwierzytelniania i bramy

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik FTP](data-factory-ftp-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawem danych usługi FTP, ustaw **typu** zestawu danych do **FileShare**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Sub ścieżkę do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** do folderu ścieżki oparte na wycinku rozpoczęcia/zakończenia daty i godziny. |Yes 
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku będzie poniżej tego formatu: <br/><br/>Dane. <Guid>.txt (przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| obiektu fileFilter |Określ filtr służący do wybierania podzbioru pliki w ścieżce folderu, a nie wszystkie pliki.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych z udziału plików. Ta właściwość nie jest obsługiwana z systemu plików HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwę pliku dla czasu serii danych. Na przykład folderPath sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**; i są obsługiwane poziomy: **optymalna** i **najszybszym**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy używany tryb transferu binarnego. Wartość true dla trybie binarnym i wartość false ASCII. Wartość domyślna: wartość True. Ta właściwość jest używana tylko w przypadku typu skojarzonej połączonej usługi typu: SerwerFTP. |Nie |

> [!NOTE]
> Nie można jednocześnie używać nazwy pliku i obiektu fileFilter.

#### <a name="example"></a>Przykład

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik FTP](data-factory-ftp-connector.md#dataset-properties) artykułu.

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w przypadku działania kopiowania
Jeśli dane są kopiowane z serwera FTP, ustaw **typ źródła** działania kopiowania do **FileSystemSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. |Wartość true, False (ustawienie domyślne) |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznik FTP](data-factory-ftp-connector.md#copy-activity-properties) artykułu.


## <a name="hdfs"></a>SYSTEM PLIKÓW HDFS

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować HDFS połączonej usługi, ustaw **typu** połączonej usługi, aby **Hdfs**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi mieć ustawioną: **Hdfs** |Yes |
| Url |Adres URL do systemu plików HDFS |Yes |
| Typ authenticationType |Anonimowe lub Windows. <br><br> Umożliwia **uwierzytelnianie Kerberos** łącznika systemu plików HDFS, można znaleźć w temacie [w tej sekcji](#use-kerberos-authentication-for-hdfs-connector) do odpowiednio skonfigurowane w lokalnym środowisku. |Yes |
| userName |Uwierzytelnianie nazwy użytkownika dla systemu Windows. |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło dla uwierzytelniania systemu Windows. |Tak (w przypadku uwierzytelniania systemu Windows) |
| gatewayName |Nazwa bramy, która powinna być używana do nawiązania połączenia systemu plików HDFS usługi fabryka danych. |Yes |
| encryptedCredential |[Nowy AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) poświadczeń dostępu do danych wyjściowych. |Nie |

#### <a name="example-using-anonymous-authentication"></a>Przykład: Przy użyciu uwierzytelniania anonimowego

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Przykład: Przy użyciu uwierzytelniania systemu Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika systemu plików HDFS](#data-factory-hdfs-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych systemu plików HDFS, ustaw **typu** zestawu danych do **FileShare**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Na przykład: folder\subfolder, określ folder\\\\podfolderów i dla d:\samplefolder, określ d:\\\\folder_przykładowy.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** do folderu ścieżki oparte na wycinku rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku będzie poniżej tego formatu: <br/><br/>Dane. <Guid>.txt (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwę pliku dla czasu serii danych. Przykład: folderPath sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można jednocześnie używać nazwy pliku i obiektu fileFilter.

#### <a name="example"></a>Przykład

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika systemu plików HDFS](#data-factory-hdfs-connector.md#dataset-properties) artykułu. 

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w przypadku działania kopiowania
Jeśli dane są kopiowane z systemu plików HDFS, ustaw **typ źródła** działania kopiowania do **FileSystemSource**, a następnie określ następujące właściwości w **źródła** sekcji:

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. |Wartość true, False (ustawienie domyślne) |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika systemu plików HDFS](#data-factory-hdfs-connector.md#copy-activity-properties) artykułu.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować SFTP połączonej usługi, ustaw **typu** połączonej usługi, aby **Sftp**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- | --- |
| host | Nazwa lub adres IP serwera SFTP. |Yes |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| Typ authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **podstawowe**, **parametry SshPublicKey**. <br><br> Zapoznaj się [uwierzytelnianie podstawowe Using](#using-basic-authentication) i [przy użyciu publicznego klucza uwierzytelniania SSH](#using-ssh-public-key-authentication) odpowiednio sekcje więcej właściwości i przykłady JSON. |Yes |
| skipHostKeyValidation | Określ, czy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Podaj odcisk palca klucza hosta. | Tak, jeśli `skipHostKeyValidation` ma wartość false.  |
| gatewayName |Nazwa bramy zarządzania danymi do nawiązywania połączenia z serwerem lokalnym SFTP. | Tak, jeśli kopiowanie danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia dostępu do serwera SFTP. Wygenerowany automatycznie po określeniu w kreatorze kopiowania lub w oknie podręcznym ClickOnce uwierzytelnianie podstawowe (nazwy użytkownika i hasła) lub uwierzytelniania parametry SshPublicKey (nazwy użytkownika i ścieżki do klucza prywatnego lub zawartości). | Nie. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |

#### <a name="example-using-basic-authentication"></a>Przykład: Przy użyciu uwierzytelniania podstawowego

Aby uwierzytelnianie podstawowe, należy ustawić `authenticationType` jako `Basic`i określ następujące właściwości poza łącznika SFTP ogólnego z nich wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- | --- |
| nazwa użytkownika | Użytkownik, który ma dostęp do serwera SFTP. |Yes |
| hasło | Hasło dla użytkownika (username). | Yes |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: **uwierzytelnianie podstawowe z zaszyfrowanych poświadczeniach**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Przy użyciu uwierzytelniania klucza publicznego SSH:**

Aby uwierzytelnianie podstawowe, należy ustawić `authenticationType` jako `SshPublicKey`i określ następujące właściwości poza łącznika SFTP ogólnego z nich wprowadzone w ostatniej sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- | --- |
| nazwa użytkownika |Użytkownik, który ma dostęp do serwera SFTP |Yes |
| privateKeyPath | Określ ścieżka bezwzględna do pliku klucza prywatnego może dostęp do tej bramy. | Określ `privateKeyPath` lub `privateKeyContent`. <br><br> Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |
| privateKeyContent | Ciąg serializacji zawartość klucza prywatnego. Kreator kopiowania można odczytać pliku klucza prywatnego i Wyodrębnij zawartość klucza prywatnego automatycznie. Jeśli używane są wszystkie inne narzędzie/pakiet SDK, należy użyć właściwości privateKeyPath. | Określ `privateKeyPath` lub `privateKeyContent`. |
| Hasło | Określ przebiegu frazy/hasło do odszyfrowania klucza prywatnego, jeśli plik klucza jest chroniony przez hasło. | Tak, jeśli hasło jest chroniony plik klucza prywatnego. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: **parametry SshPublicKey uwierzytelniania za pomocą prywatnego klucza zawartości**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika SFTP](data-factory-sftp-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować SFTP zestawu danych, ustaw **typu** zestawu danych do **FileShare**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Sub ścieżkę do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** do folderu ścieżki oparte na wycinku rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku będzie poniżej tego formatu: <br/><br/>Dane. <Guid>.txt (przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| obiektu fileFilter |Określ filtr służący do wybierania podzbioru pliki w ścieżce folderu, a nie wszystkie pliki.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych z udziału plików. Ta właściwość nie jest obsługiwana z systemu plików HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwę pliku dla czasu serii danych. Na przykład folderPath sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ, czy używany tryb transferu binarnego. Wartość true dla trybie binarnym i wartość false ASCII. Wartość domyślna: wartość True. Ta właściwość jest używana tylko w przypadku typu skojarzonej połączonej usługi typu: SerwerFTP. |Nie |

> [!NOTE]
> Nie można jednocześnie używać nazwy pliku i obiektu fileFilter.

#### <a name="example"></a>Przykład

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika SFTP](data-factory-sftp-connector.md#dataset-properties) artykułu. 

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w przypadku działania kopiowania
Jeśli dane są kopiowane z użyciem protokołu SFTP źródła, ustaw **typ źródła** działania kopiowania do **FileSystemSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. |Wartość true, False (ustawienie domyślne) |Nie |



#### <a name="example"></a>Przykład

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika SFTP](data-factory-sftp-connector.md#copy-activity-properties) artykułu.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować HTTP połączonej usługi, ustaw **typu** połączonej usługi, aby **Http**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| url | Podstawowy adres URL do serwera sieci Web | Yes |
| Typ authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to: **anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, **ClientCertificate**. <br><br> Odpowiednio można znaleźć w sekcjach poniżej tej tabeli na więcej właściwości i przykłady JSON dla tych typów uwierzytelniania. | Yes |
| enableServerCertificateValidation | Określ, czy włączyć weryfikacji certyfikatu serwera SSL, jeśli źródło jest serwer sieci Web protokołu HTTPS | Nie, domyślna to true |
| gatewayName | Nazwa bramy zarządzania danymi do łączenia się z lokalnym źródłem HTTP. | Tak, jeśli kopiowanie danych z lokalnego źródła HTTP. |
| encryptedCredential | Zaszyfrowane poświadczenia można uzyskać dostępu do punktu końcowego HTTP. Wygenerowany automatycznie podczas konfigurowania informacji o uwierzytelnianiu w kreatorze kopiowania lub w oknie podręcznym ClickOnce. | Nie. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Przykład: Uwierzytelnianie podstawowe, szyfrowane lub systemu Windows
Ustaw `authenticationType` jako `Basic`, `Digest`, lub `Windows`i określ następujące właściwości poza łącznika HTTP ogólnego z nich wprowadzono powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| nazwa użytkownika | Nazwa użytkownika do uzyskania dostępu punkt końcowy HTTP. | Yes |
| hasło | Hasło dla użytkownika (username). | Yes |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Przykład: Przy użyciu uwierzytelniania ClientCertificate

Aby uwierzytelnianie podstawowe, należy ustawić `authenticationType` jako `ClientCertificate`i określ następujące właściwości poza łącznika HTTP ogólnego z nich wprowadzono powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| embeddedCertData | Zawartość algorytmem Base64 danych binarnych z pliku wymiany informacji osobistych (PFX). | Określ `embeddedCertData` lub `certThumbprint`. |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany na komputerze bramy magazynu certyfikatów. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego źródła HTTP. | Określ `embeddedCertData` lub `certThumbprint`. |
| hasło | Hasło skojarzone z certyfikatem. | Nie |

Jeśli używasz `certThumbprint` dla uwierzytelniania i certyfikat jest zainstalowany w magazynie osobistym komputera lokalnego, należy udzielić uprawnień do odczytu do usługi bramy:

1. Uruchom program Microsoft Management Console (MMC). Dodaj **certyfikaty** przystawki przeznaczonego **komputera lokalnego**.
2. Rozwiń węzeł **certyfikaty**, **osobistych**i kliknij przycisk **certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat z magazynu osobistego i wybierz **wszystkie zadania**->**Zarządzaj kluczami prywatnymi...**
3. Na **zabezpieczeń** , Dodaj konto użytkownika, pod którą jest uruchomiona usługa hosta bramy zarządzania danymi z dostępem do odczytu do certyfikatu.  

**Przykład: przy użyciu certyfikatu klienta:** to połączone usługi łączy fabrykę danych z lokalnego serwera sieci web HTTP. Używa certyfikatu klienta, który jest instalowany na komputerze z zainstalowana brama zarządzania danymi.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Przykład: przy użyciu certyfikatu klienta w pliku
Łącza usługi to połączone fabrykę danych z lokalnego serwera sieci web HTTP. Za pomocą pliku certyfikatu klienta na komputer i zainstalowana brama zarządzania danymi.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika HTTP](data-factory-http-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych HTTP, ustaw **typu** zestawu danych do **Http**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ścieżka nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. <br><br> Aby utworzyć dynamicznego adresu URL, można użyć [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md), przykład: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **UZYSKAĆ** lub **POST**. | Nie. Wartość domyślna to `GET`. |
| additionalHeaders | Dodatkowych nagłówków żądania HTTP. | Nie |
| requestBody | Treść żądania HTTP. | Nie |
| Format | Jeśli chcesz po prostu **pobierają dane z punktu końcowego HTTP jako — jest** bez podczas analizowania, Pomiń ten ustawienia formatu. <br><br> Jeśli chcesz przeanalizować zawartości odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example-using-the-get-default-method"></a>Przykład: używających metody GET (ustawienie domyślne)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Przykład: przy użyciu metody POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Aby uzyskać więcej informacji, zobacz [łącznika HTTP](data-factory-http-connector.md#dataset-properties) artykułu.

### <a name="http-source-in-copy-activity"></a>Źródła HTTP w przypadku działania kopiowania
Jeśli dane są kopiowane ze źródła HTTP, ustaw **typ źródła** działania kopiowania do **HttpSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu (TimeSpan) dla żądania HTTP można uzyskać odpowiedzi. Limit czasu jest uzyskanie odpowiedzi nie limitu czasu można odczytać danych odpowiedzi. | Nie. Wartość domyślna: 00:01:40 |


#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika HTTP](data-factory-http-connector.md#copy-activity-properties) artykułu.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować OData połączonej usługi, ustaw **typu** połączonej usługi, aby **OData**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| url |Adres URL usługi OData. |Yes |
| Typ authenticationType |Typ uwierzytelniania używany do nawiązania połączenia źródła OData. <br/><br/> Chmury OData możliwe wartości to anonimowe, podstawowe i OAuth (Uwaga obecnie tylko pomocy technicznej usługi fabryka danych Azure OAuth opartej na usłudze Azure Active Directory). <br/><br/> Dla protokołu OData lokalnymi możliwe wartości to anonimowe, podstawowe i systemu Windows. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Tak (tylko wtedy, gdy używasz uwierzytelniania podstawowego) |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Tak (tylko wtedy, gdy używasz uwierzytelniania podstawowego) |
| authorizedCredential |Jeśli używasz uwierzytelniania OAuth, kliknij przycisk **autoryzacji** przycisku w kreatorze kopiowania fabryki danych lub edytorze, a następnie wprowadź Twoje poświadczenia wartość tej właściwości będzie wygenerowany automatycznie. |Tak (tylko wtedy, gdy używasz uwierzytelniania OAuth) |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną usługą OData. Określ tylko, jeśli dane są kopiowane z lokalnego źródła OData. |Nie |

#### <a name="example---using-basic-authentication"></a>Przykład — przy użyciu uwierzytelniania podstawowego
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Przykład — przy użyciu uwierzytelniania anonimowego

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Przykład — Windows przy użyciu uwierzytelniania dostępu do lokalnego źródła OData

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Przykład — przy użyciu uwierzytelniania OAuth, uzyskiwanie dostępu do chmury źródło OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika OData](data-factory-odata-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych OData, ustaw **typu** zestawu danych do **ODataResource**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| ścieżka |Ścieżka do zasobu OData |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika OData](data-factory-odata-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane ze źródła danych OData, ustaw **typ źródła** działania kopiowania do **RelationalSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Przykład | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |"?$select=Name, Description&$top=5" |Nie |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika OData](data-factory-odata-connector.md#copy-activity-properties) artykułu.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować ODBC połączonej usługi, ustaw **typu** połączonej usługi, aby **OnPremisesOdbc**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Poświadczenie dostępu z systemem innym niż część ciąg połączenia i opcjonalnie zaszyfrowane poświadczenia. Przykłady w poniższych sekcjach. |Yes |
| poświadczenia |Dostęp do poświadczeń część ciągu połączenia określonego w formacie wartości właściwości sterownika. Przykład: "Uid =<user ID>; Pwd =<password>; RefreshToken =<secret refresh token>; ". |Nie |
| Typ authenticationType |Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC. Możliwe wartości to: anonimowych, jak i podstawowych. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych do połączenia z magazynem danych ODBC. |Yes |

#### <a name="example---using-basic-authentication"></a>Przykład — przy użyciu uwierzytelniania podstawowego

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Przykład — użyciu zaszyfrowane poświadczenia uwierzytelniania podstawowego
Można szyfrować poświadczeń przy użyciu [AzureRMDataFactoryEncryptValue nowy](https://msdn.microsoft.com/library/mt603802.aspx) polecenia cmdlet (w wersji 1.0 programu Azure PowerShell) lub [AzureDataFactoryEncryptValue nowy](https://msdn.microsoft.com/library/dn834940.aspx) (0,9 lub starszej wersji platformy Azure PowerShell).  

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Przykład: Przy użyciu uwierzytelniania anonimowego

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika ODBC](data-factory-odbc-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych ODBC, ustaw **typu** zestawu danych do **RelationalTable**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w magazynie danych ODBC. |Yes |


#### <a name="example"></a>Przykład

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika ODBC](data-factory-odbc-connector.md#dataset-properties) artykułu. 

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z magazynu danych ODBC, ustaw **typ źródła** działania kopiowania do **RelationalSource**i określ następujące właściwości w **źródła** sekcji :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
``` 

Aby uzyskać więcej informacji, zobacz [łącznika ODBC](data-factory-odbc-connector.md#copy-activity-properties) artykułu.

## <a name="salesforce"></a>SalesForce


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Salesforce połączonej usługi, ustaw **typu** połączonej usługi, aby **Salesforce**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| environmentUrl | Określ wystąpienie adres URL usługi Salesforce. <br><br> -Domyślna to "https://login.salesforce.com". <br> -Aby skopiować dane z piaskownicy, określ "https://test.salesforce.com". <br> -Aby skopiować dane z domeny niestandardowej, określ, na przykład "https://[domain].my.salesforce.com". |Nie |
| nazwa użytkownika |Określ nazwę użytkownika dla konta użytkownika. |Yes |
| hasło |Określ hasło dla konta użytkownika. |Yes |
| securityToken |Określ tokenu zabezpieczającego dla konta użytkownika. Zobacz [uzyskać token zabezpieczeń](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) instrukcje dotyczące resetowania/Get tokenu zabezpieczającego. Aby dowiedzieć się więcej o tokeny zabezpieczające ogólnie rzecz biorąc, zobacz [zabezpieczeń i interfejsu API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usług Salesforce](data-factory-salesforce-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować Salesforce zestawu danych, ustaw **typu** zestawu danych do **RelationalTable**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w usłudze Salesforce. |Nie (Jeśli **zapytania** z **RelationalSource** jest określona) |

#### <a name="example"></a>Przykład

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usług Salesforce](data-factory-salesforce-connector.md#dataset-properties) artykułu. 

### <a name="relational-source-in-copy-activity"></a>Relacyjnego źródła w przypadku działania kopiowania
Jeśli dane są kopiowane z usług Salesforce, ustaw **typ źródła** działania kopiowania do **RelationalSource**i określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj niestandardowych zapytania można odczytać danych. |Zapytania SQL 92 lub [Salesforce obiektu Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) zapytania. Przykład: `select * from MyTable__c`. |Nie (Jeśli **tableName** z **dataset** jest określona) |

#### <a name="example"></a>Przykład  



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnych niestandardowych obiektów.

Aby uzyskać więcej informacji, zobacz [łącznika usług Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) artykułu. 

## <a name="web-data"></a>Dane sieci Web 

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować sieci Web połączonej usługi, ustaw **typu** połączonej usługi, aby **sieci Web**i określ następujące właściwości w **typeProperties** sekcji:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Url |Adres URL źródła w sieci Web |Yes |
| Typ authenticationType |Anonimowe. |Yes |
 

#### <a name="example"></a>Przykład


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika tabeli Web](data-factory-web-table-connector.md#linked-service-properties) artykułu. 

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestawu danych w sieci Web, ustaw **typu** zestawu danych do **tabeli WebTable**i określ następujące właściwości w **typeProperties** sekcji: 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Typ zestawu danych. należy wybrać opcję **tabeli WebTable** |Yes |
| ścieżka |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie jest określona, używana jest tylko adres URL określony w definicji połączonej usługi. |
| indeks |Indeks tabeli w zasobie. Zobacz [Get indeksu tabeli na stronie HTML](#get-index-of-a-table-in-an-html-page) sekcji, aby instrukcje dotyczące pobierania indeksu tabeli na stronie HTML. |Yes |

#### <a name="example"></a>Przykład

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika tabeli Web](data-factory-web-table-connector.md#dataset-properties) artykułu. 

### <a name="web-source-in-copy-activity"></a>Źródło w sieci Web w przypadku działania kopiowania
Jeśli dane są kopiowane z tabeli sieci web, należy ustawić **typ źródła** działania kopiowania do **WebSource**. Obecnie, gdy źródła w przypadku działania kopiowania jest typu **WebSource**, są obsługiwane żadne dodatkowe właściwości.

#### <a name="example"></a>Przykład

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika tabeli Web](data-factory-web-table-connector.md#copy-activity-properties) artykułu. 

## <a name="compute-environments"></a>ŚRODOWISKA OBLICZENIOWE
W poniższej tabeli wymieniono środowiska obliczeniowe obsługiwane przez fabryki danych i działań przekształcania, które można uruchomić na nich. Kliknij łącze do obliczeń, który chcesz wyświetlić schematów JSON połączonej usługi połączyć je z fabryki danych. 

| Środowisko obliczeniowe | Działania |
| --- | --- |
| [Klaster usługi HDInsight na żądanie](#on-demand-azure-hdinsight-cluster) lub [klastrem usługi HDInsight](#existing-azure-hdinsight-cluster) |[Działania niestandardowego .NET](#net-custom-activity), [Hive działania](#hdinsight-hive-activity), [wieprzowa działania] (#hdinsight-pig działania [działania MapReduce](#hdinsight-mapreduce-activity), [przesyłanie strumieniowe działania Hadoop](#hdinsight-streaming-activityd), [ Działanie Spark](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Niestandardowe działanie platformy .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Działanie wykonywania wsadowego uczenia maszynowego](#machine-learning-batch-execution-activity), [działanie aktualizacji zasobu uczenia maszynowego](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Język U-SQL usługi Data Lake Analytics](#data-lake-analytics-u-sql-activity) |
| [Baza danych Azure SQL](#azure-sql-database-1), [magazyn danych Azure SQL](#azure-sql-data-warehouse-1), [programu SQL Server](#sql-server-1) |[Procedura składowana](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Klaster Azure HDInsight na żądanie
Usługi fabryka danych Azure może automatycznie tworzyć opartych na systemie Windows/Linux klastra usługi HDInsight na żądanie do przetwarzania danych. Klaster jest tworzony w tym samym regionie co skojarzone z klastrem konta magazynu (właściwość linkedServiceName w formacie JSON). Można uruchamiać następujących działań transformacji na tej połączonej usługi: [działania niestandardowego .NET](#net-custom-activity), [Hive działania](#hdinsight-hive-activity), [wieprzowa działania] (#hdinsight-pig działania [działania MapReduce ](#hdinsight-mapreduce-activity), [Przesyłanie strumieniowe działania Hadoop](#hdinsight-streaming-activityd), [Spark działania](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Połączona usługa 
Poniższa tabela zawiera opisy właściwości używane w definicji Azure JSON usługi HDInsight połączony na żądanie.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Powinien mieć ustawioną właściwość type **HDInsightOnDemand**. |Yes |
| clusterSize |Liczba węzłów procesu roboczego/danych w klastrze. Klaster usługi HDInsight jest tworzony z głównymi węzłami 2 wraz z liczbą węzłów procesu roboczego, które określisz dla tej właściwości. Węzły mają rozmiar Standard_D3, który ma 4 rdzenie, więc klastra z węzłem procesu roboczego 4 przyjmuje 24 rdzenie (4\*4 = 16 rdzenie dla węzłów procesu roboczego, a także 2\*rdzenie 4 = 8 dla węzłów głównych). Zobacz [utworzyć Linux opartych klastrów Hadoop w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) szczegółowe informacje na temat warstwy Standard_D3. |Yes |
| wartość TimeToLive |Limit czasu bezczynności klastra usługi HDInsight na żądanie. Określa, jak długo klastra usługi HDInsight na żądanie pozostaje aktywne po zakończeniu działania uruchamiania, jeśli w klastrze nie ma żadnych aktywnych działań.<br/><br/>Na przykład jeśli uruchomienia działania trwa 6 minut i timetolive jest ustawiony na 5 minut, klaster pozostanie aktywności 5 minut po uruchomieniu 6 minut przetwarzania działania. Jeśli inny uruchamiania działania jest wykonywane z okna 6 minut, jednak jest przetwarzany przez tego samego klastra.<br/><br/>Tworzenie klastra usługi HDInsight na żądanie jest kosztowna operacja (może to potrwać pewien czas), użyj tak, to ustawienie jako potrzebne do zwiększenia wydajności fabryki danych przez ponowne użycie klastra usługi HDInsight na żądanie.<br/><br/>Jeśli wartość timetolive jest ustawiona na 0, klastra jest usuwany natychmiast uruchomić działanie w przetworzonej. Z drugiej strony Jeśli ustawisz wysokiej wartości klastra może pozostać bezczynny, co niepotrzebnie wysokich kosztów. Dlatego jest ważne, aby ustawić odpowiednią wartość, na podstawie Twoich potrzeb.<br/><br/>Wiele potoki mogą współużytkować tego samego wystąpienia klastra usługi HDInsight na żądanie w przypadku skonfigurowana wartość timetolive właściwości |Yes |
| wersja |Wersja klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje usługi HDInsight w fabryce danych Azure](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nie |
| linkedServiceName |Azure połączonej usługi magazynu do użycia przez klaster na żądanie do przechowywania i przetwarzania danych. <p>Obecnie nie można utworzyć klastra usługi HDInsight na żądanie, która używa usługi Azure Data Lake Store jako magazynu. Jeśli chcesz przechowywać dane wynikowe z HDInsight przetwarzania w usłudze Azure Data Lake Store, umożliwia działanie kopiowania skopiować dane z magazynu obiektów Blob Azure do usługi Azure Data Lake Store.</p>  | Yes |
| additionalLinkedServiceNames |Określa, że dodatkowe konta magazynu dla usługi HDInsight połączonej usługi, dzięki czemu usługi fabryka danych można zarejestrować je w Twoim imieniu. |Nie |
| osType |Typ systemu operacyjnego. Dozwolone wartości to: (domyślnie) systemu Windows i Linux |Nie |
| hcatalogLinkedServiceName |Nazwa programu Azure SQL połączonej usługi, które HCatalog bazy danych. Klaster usługi HDInsight na żądanie jest tworzona przy użyciu bazy danych Azure SQL jako potrzeby magazynu metadanych. |Nie |

### <a name="json-example"></a>Przykład JSON
Następujące JSON definiuje opartych na systemie Linux usługi HDInsight połączony na żądanie. Usługi fabryka danych automatycznie tworzy **opartych na systemie Linux** klastra usługi HDInsight podczas przetwarzania wycinka danych. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [obliczeniowe połączonych usług](data-factory-compute-linked-services.md) artykułu. 

## <a name="existing-azure-hdinsight-cluster"></a>Istniejący klaster Azure HDInsight
Można utworzyć usługi Azure HDInsight połączony do zarejestrowania klastrem usługi HDInsight przy użyciu fabryki danych. Można wykonać następujące działania przekształcania danych na tej połączonej usługi: [działania niestandardowego .NET](#net-custom-activity), [Hive działania](#hdinsight-hive-activity), [wieprzowa działania] (#hdinsight-pig działania [MapReduce działanie](#hdinsight-mapreduce-activity), [przesyłanie strumieniowe działania Hadoop](#hdinsight-streaming-activityd), [Spark działania](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używane w definicji Azure JSON usługi Azure HDInsight połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Powinien mieć ustawioną właściwość type **HDInsight**. |Yes |
| clusterUri |Identyfikator URI klastra usługi HDInsight. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, który ma być używany do nawiązania połączenia z istniejącym klastrze usługi HDInsight. |Yes |
| hasło |Określ hasło dla konta użytkownika. |Yes |
| linkedServiceName | Nazwa usługi Azure Storage połączone usługi, która odwołuje się do magazynu obiektów blob platformy Azure, używane przez klaster usługi HDInsight. <p>Obecnie nie można określić, czy usługa Azure Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster usługi HDInsight ma dostęp do usługi Data Lake Store może dostęp do danych w usłudze Azure Data Lake Store ze skryptów gałęzi/Pig. </p>  |Yes |

Dla wersji obsługiwane klastrów usługi HDInsight, zobacz [obsługiwane wersje HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Usługa partii zadań Azure połączony do zarejestrowania puli partii maszynach wirtualnych (VM) można utworzyć przy użyciu fabryki danych. Możesz uruchomić niestandardowych działań platformy .NET przy użyciu partii zadań Azure lub usługi Azure HDInsight. Można uruchomić [działania niestandardowego .NET](#net-custom-activity) na tej połączonej usługi. 

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używane w definicji Azure JSON usługi partia zadań Azure połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Powinien mieć ustawioną właściwość type **AzureBatch**. |Yes |
| accountName |Nazwa konta partii zadań Azure. |Yes |
| accessKey |Klucz dostępu dla konta usługi partia zadań Azure. |Yes |
| poolName |Nazwa puli maszyn wirtualnych. |Yes |
| linkedServiceName |Nazwa usługi Azure Storage połączonej usługi skojarzone z tą usługą partii zadań Azure połączone. Tej połączonej usługi jest używany dla tymczasowych plików wymaganych do uruchomienia działania i przechowywanie dzienniki wykonywania działania. |Yes |


#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Można utworzyć usługi Azure Machine Learning, połączone można zarejestrować punktu końcowego z fabryką danych oceniania partii uczenia maszynowego. Dwa przekształcania działań, które można uruchomić na tym połączonej usługi: [działanie wykonywania wsadowego przez Machine Learning](#machine-learning-batch-execution-activity), [Machine Learning aktualizacji zasobów działania](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opisy właściwości używane w definicji Azure JSON usługi Azure Machine Learning połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Typ |Powinien mieć ustawioną właściwość type: **uczenie maszynowe Azure**. |Yes |
| mlEndpoint |Adres URL wsadowego oceniania. |Yes |
| apiKey |Interfejs API modelu opublikowanych obszaru roboczego. |Yes |

#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć z usługą Azure Data Lake Analytics obliczeniowe usługi fabryka danych Azure, przed użyciem [działanie U-SQL w programie Data Lake Analytics](data-factory-usql-activity.md) w potoku.

### <a name="linked-service"></a>Połączona usługa

Poniższa tabela zawiera opisy właściwości używane w definicji JSON usługi Azure Data Lake Analytics połączone. 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Typ |Powinien mieć ustawioną właściwość type: **AzureDataLakeAnalytics**. |Yes |
| accountName |Nazwa konta usługi Azure Data Lake Analytics. |Yes |
| dataLakeAnalyticsUri |Identyfikator URI, usługi Azure Data Lake Analytics. |Nie |
| Autoryzacji |Kod autoryzacji jest automatycznie pobierany po kliknięciu przycisku **autoryzacji** przycisk Edytor fabryki danych i ukończeniu operacji logowania OAuth. |Yes |
| subscriptionId |Identyfikator subskrypcji platformy Azure |Nie (Jeśli nie zostanie określony, używany subskrypcji fabryki danych). |
| resourceGroupName |Nazwa grupy zasobów platformy Azure |Nie (Jeśli nie zostanie określony, używana grupa zasobów z fabryką danych). |
| Identyfikator sesji |Identyfikator sesji z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. Gdy używasz Edytor fabryki danych, ten identyfikator jest generowane automatycznie. |Yes |


#### <a name="json-example"></a>Przykład JSON
W poniższym przykładzie przedstawiono definicję JSON dla usługi Azure Data Lake Analytics połączone.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Azure SQL Database
Tworzenie Azure połączoną usługą SQL i użyj go przy użyciu [działania dotyczącego procedury składowanej](#stored-procedure-activity) aby wywołać procedurę składowaną z potoku fabryki danych. 

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować bazy danych SQL Azure połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDatabase**i określ następujące właściwości w **typeProperties** sekcja:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Podaj informacje wymagane do połączenia z wystąpieniem bazy danych SQL Azure dla właściwości connectionString. |Yes |

#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Zobacz [Łącznik usług SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties) artykułu, aby uzyskać szczegółowe informacje o tej połączonej usługi.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Tworzenie usługi Azure SQL Data Warehouse połączone i używać go z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku fabryki danych. 

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Azure SQL Data Warehouse połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDW**i określ następujące właściwości w **typeProperties** sekcja:  

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Parametry połączenia |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse właściwości connectionString. |Yes |

#### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artykułu. 

## <a name="sql-server"></a>Oprogramowanie SQL Server 
Tworzenie usługi SQL Server połączone i używać go z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku fabryki danych. 

### <a name="linked-service"></a>Połączona usługa
Tworzenie połączonej usługi typu **OnPremisesSqlServer** do połączenia z lokalną bazą danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis specyficzne dla lokalnej usługi SQL Server połączone elementy JSON.

Poniższa tabela zawiera opis specyficzne dla usługi SQL Server połączone elementy JSON.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Powinien mieć ustawioną właściwość type: **OnPremisesSqlServer**. |Yes |
| Parametry połączenia |Określ connectionString informacje wymagane do połączenia z lokalną bazą danych programu SQL Server, przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Yes |
| gatewayName |Nazwa bramy, która powinna być używana przez usługi fabryka danych nawiązać połączenia z lokalną bazą danych programu SQL Server. |Yes |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **domainname\\username**. |Nie |
| hasło |Określ hasło dla konta użytkownika, określone nazwy użytkownika. |Nie |

Można szyfrować poświadczeń przy użyciu **AzureRmDataFactoryEncryptValue nowy** polecenia cmdlet i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (**EncryptedCredential** właściwość):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: JSON dla przy użyciu uwierzytelniania programu SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: JSON dla przy użyciu uwierzytelniania systemu Windows

Jeśli podano nazwę użytkownika i hasło, bramy służą one do personifikacja określonego konta użytkownika nawiązać połączenia z lokalną bazą danych programu SQL Server. W przeciwnym razie brama łączy się z serwerem SQL, bezpośrednio z kontekstem zabezpieczeń bramy (jego konta uruchamiania).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [łącznika programu SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artykułu.

## <a name="data-transformation-activities"></a>DZIAŁANIA PRZEKSZTAŁCENIA DANYCH

Działanie | Opis
-------- | -----------
[Działanie HDInsight Hive](#hdinsight-hive-activity) | HDInsight Hive działania w potoku fabryki danych wykonuje zapytań programu Hive samodzielnie lub w klastrze systemu Windows/Linux-based HDInsight na żądanie. 
[Działanie HDInsight Pig](#hdinsight-pig-activity) | HDInsight Pig działania w potoku fabryki danych wykonuje zapytania Pig samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie.
[Działania technologii MapReduce w usłudze HDInsight](#hdinsight-mapreduce-activity) | HDInsight MapReduce działania w potoku fabryki danych wykonuje programy MapReduce samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie.
[Działania przesyłania strumieniowego usługi HDInsight](#hdinsight-streaming-activity) | HDInsight działaniu przesyłania strumieniowego w potoku fabryki danych wykonuje przesyłania strumieniowego usługi Hadoop programy samodzielnie lub klastra systemu Windows/Linux-based HDInsight na żądanie.
[Działania platformy Spark w usłudze HDInsight](#hdinsight-spark-activity) | HDInsight Spark działania w potoku fabryki danych wykonuje programy Spark w klastrze usługi HDInsight. 
[Działanie wykonywania wsadowego w usłudze Machine Learning](#machine-learning-batch-execution-activity) | Fabryka danych Azure umożliwia łatwe tworzenie potoków korzystających z opublikowanych usługi sieci web Azure Machine Learning analizy predykcyjnej. Przy użyciu działanie wykonywania wsadowego w potoku fabryki danych Azure, można wywołać usługi sieci web usługi Machine Learning w celu tworzenia prognoz danych w partii. 
[Działania aktualizowania zasobów w usłudze Machine Learning](#machine-learning-update-resource-activity) | Wraz z upływem czasu modeli predykcyjnych w uczeniu maszynowym oceniania eksperymenty konieczne retrained, przy użyciu nowych baz danych wejściowych. Po wykonaniu ponownego trenowania chcesz zaktualizować usługę sieci web oceniania retrained modelu uczenia maszynowego. Działanie aktualizacji zasobu służy do aktualizowania usługi sieci web z nowo trenowanego modelu.
[Działania procedur składowanych](#stored-procedure-activity) | Działania procedury składowanej w potoku fabryki danych służy do wywołania procedury przechowywanej w jednym z następujących magazynów danych: baza danych SQL Azure, Magazyn danych SQL Azure, bazy danych serwera SQL w przedsiębiorstwie lub maszynie Wirtualnej platformy Azure. 
[Data Lake Analytics U-SQL działania](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL działanie uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics.  
[Niestandardowe działanie platformy .NET](#net-custom-activity) | Do przekształcania danych w taki sposób, który nie jest obsługiwany przez fabrykę danych należy można tworzyć niestandardowe działania na własną logikę przetwarzania danych i użyj działania w potoku. Można skonfigurować niestandardowe działania .NET przy użyciu usługi partia zadań Azure lub klaster Azure HDInsight. 

     
## <a name="hdinsight-hive-activity"></a>Działania technologii Hive w usłudze HDInsight
Można określić następujące właściwości w definicji Hive JSON działania. Właściwość typu działania musi być: **HDInsightHive**. Należy najpierw utworzyć usługi HDInsight połączone i określ nazwę jej jako wartości dla **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightHive:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| skrypt |Określ wbudowanego skryptu Hive |Nie |
| Ścieżka skryptu |Przechowywanie skryptu Hive w magazynie obiektów blob platformy Azure i podaj ścieżkę do pliku. Użyj właściwości 'script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary klucz wartość dla odwołania do skryptu Hive za pomocą "hiveconf" |Nie |

Te właściwości typu są specyficzne dla działania Hive. Inne właściwości (poza sekcji typeProperties) są obsługiwane dla wszystkich działań.   

### <a name="json-example"></a>Przykład JSON
Następujące JSON definiuje działania HDInsight Hive w potoku.  

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Aby uzyskać więcej informacji, zobacz [Hive działania](data-factory-hive-activity.md) artykułu. 

## <a name="hdinsight-pig-activity"></a>Działania technologii Pig w usłudze HDInsight
Można określić następujące właściwości w definicji Pig działania w formacie JSON. Właściwość typu działania musi być: **HDInsightPig**. Należy najpierw utworzyć usługi HDInsight połączone i określ nazwę jej jako wartości dla **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightPig: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| skrypt |Określ wbudowanego skryptu Pig |Nie |
| Ścieżka skryptu |Umieść skrypt programu Pig w magazynie obiektów blob platformy Azure, a następnie podaj ścieżkę do pliku. Użyj właściwości 'script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary klucz wartość dla odwołania do skryptu Pig |Nie |

Te właściwości typu są specyficzne dla działania Pig. Inne właściwości (poza sekcji typeProperties) są obsługiwane dla wszystkich działań.   

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Aby uzyskać więcej informacji, zobacz [działania Pig](#data-factory-pig-activity.md) artykułu. 

## <a name="hdinsight-mapreduce-activity"></a>Działania technologii MapReduce w usłudze HDInsight
Można określić następujące właściwości w definicji JSON działania MapReduce. Właściwość typu działania musi być: **HDInsightMapReduce**. Należy najpierw utworzyć usługi HDInsight połączone i określ nazwę jej jako wartości dla **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightMapReduce: 

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| jarLinkedService | Nazwa połączonej usługi dla usługi Azure Storage, który zawiera plik JAR. | Yes |
| jarFilePath | Ścieżka do pliku JAR w magazynie Azure. | Yes | 
| className | Nazwa klasy głównym w pliku JAR. | Yes | 
| argumenty | Lista argumentów rozdzielonych przecinkami programu MapReduce. W czasie wykonywania, zobacz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Rozróżnianie argumentów z argumentami MapReduce, należy rozważyć użycie zarówno opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe,--itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości) | Nie | 

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [działania MapReduce](data-factory-map-reduce.md) artykułu. 

## <a name="hdinsight-streaming-activity"></a>Działania przesyłania strumieniowego usługi HDInsight
Można określić następujące właściwości w definicji działania przesyłania strumieniowego usługi Hadoop w formacie JSON. Właściwość typu działania musi być: **HDInsightStreaming**. Należy najpierw utworzyć usługi HDInsight połączone i określ nazwę jej jako wartości dla **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightStreaming: 

| Właściwość | Opis | 
| --- | --- |
| mapowania | Nazwa pliku wykonywalnego mapowania. W tym przykładzie cat.exe jest mapowania pliku wykonywalnego.| 
| Reduktor | Nazwa pliku wykonywalnego reduktor. W tym przykładzie wc.exe jest reduktor pliku wykonywalnego. | 
| wejście | Plik wejściowy (w tym miejscu) dla mapowania. W tym przykładzie: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample jest kontenera obiektów blob, przykład/data/Gutenberg jest folder i davinci.txt jest obiektu blob. |
| output | Plik wyjściowy (w tym miejscu) dla reduktor. Dane wyjściowe zadania przesyłania strumieniowego usługi Hadoop jest zapisany w lokalizacji określonej dla tej właściwości. |
| filePaths | Ścieżki do plików wykonywalnych mapowania i reduktor. W tym przykładzie: "adfsample/example/apps/wc.exe" adfsample jest kontenera obiektów blob, przykład/aplikacji jest folder i wc.exe jest plikiem wykonywalnym. | 
| fileLinkedService | Usługa Azure Storage połączone usługi, która reprezentuje magazynu Azure, który zawiera pliki określone w sekcji filePaths. | 
| argumenty | Lista argumentów rozdzielonych przecinkami programu MapReduce. W czasie wykonywania, zobacz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Rozróżnianie argumentów z argumentami MapReduce, należy rozważyć użycie zarówno opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe,--itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości) | 
| getDebugInfo | Element opcjonalny. Jeśli ustawiono awarii, dzienniki są pobierane tylko w przypadku awarii. Jeśli je skonfigurowano do wszystkich, dzienniki są zawsze pobierane niezależnie od stanu wykonywania. | 

> [!NOTE]
> Należy określić wyjściowy zestaw danych działania przesyłania strumieniowego usługi Hadoop dla **generuje** właściwości. Ten zestaw danych można tylko fikcyjny zestawu danych, który jest wymagany do kierowania harmonogram potoku (co godzinę, codziennie, itp.). Jeśli działanie nie przyjmuje dane wejściowe, można pominąć, określając zestawem danych wejściowych dla działania dla **dane wejściowe** właściwości.  

## <a name="json-example"></a>Przykład JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Aby uzyskać więcej informacji, zobacz [działaniu przesyłania strumieniowego usługi Hadoop](data-factory-hadoop-streaming-activity.md) artykułu. 

## <a name="hdinsight-spark-activity"></a>Działania platformy Spark w usłudze HDInsight
Można określić następujące właściwości w definicji Spark działania w formacie JSON. Właściwość typu działania musi być: **HDInsightSpark**. Należy najpierw utworzyć usługi HDInsight połączone i określ nazwę jej jako wartości dla **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightSpark: 

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| rootPath | Kontener obiektów Blob platformy Azure i folder zawierający plik Spark. Nazwa pliku jest rozróżniana wielkość liter. | Yes |
| entryFilePath | Ścieżka względna do folderu głównego Spark kodu/pakietu. | Yes |
| className | Klasy głównym aplikacji Java/Spark | Nie | 
| argumenty | Lista argumentów wiersza polecenia do programu Spark. | Nie | 
| proxyUser | Konto użytkownika do personifikacji do wykonania programu Spark | Nie | 
| sparkConfig | Właściwości konfiguracji platformy Spark. | Nie | 
| getDebugInfo | Określa, kiedy Spark pliki dziennika są kopiowane do magazynu Azure używanego przez klaster usługi HDInsight (lub) został określony przez sparkJobLinkedService. Dozwolone wartości: None, zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie | 
| sparkJobLinkedService | Magazyn Azure połączone usługi, która ma Spark plik zadania, zależności i dzienniki.  Jeśli nie określisz wartości dla tej właściwości, Magazyn skojarzony z klastrem usługi HDInsight jest używany. | Nie |

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Pamiętaj o następujących kwestiach: 

- **Typu** właściwość jest ustawiona na **HDInsightSpark**.
- **Właściwość rootPath** ustawiono **adfspark\\pyFiles** gdzie adfspark jest kontenera obiektów Blob platformy Azure i pyFiles jest poprawnie folder, w tym kontenerze. W tym przykładzie magazynu obiektów Blob Azure jest skojarzony z klastrem Spark. Można przekazać pliku do innego magazynu Azure. Jeśli tak zrobisz, Utwórz połączoną usługą magazynu Azure, aby połączyć konto magazynu z fabryką danych. Następnie określ jako wartość dla nazwy połączonej usługi **sparkJobLinkedService** właściwości. Zobacz [właściwości działania Spark](#spark-activity-properties) szczegóły dotyczące tej właściwości oraz inne właściwości obsługiwane przez działanie Spark.
- **EntryFilePath** ustawiono **test.py**, czyli plik python. 
- **GetDebugInfo** właściwość jest ustawiona na **zawsze**, co oznacza, że pliki dziennika są zawsze generowany (powodzenie lub niepowodzenie).  

    > [!IMPORTANT]
    > Zaleca się, że nie zostały ustawione ta właściwość zawsze w środowisku produkcyjnym, chyba że są Rozwiązywanie problemów. 
- **Generuje** sekcja ma jeden wyjściowy zestaw danych. Należy określić wyjściowy zestaw danych, nawet jeśli spark program nie generuje żadnego wyniku. Wyjściowy zestaw danych dysków harmonogramu dla potoku (co godzinę, codziennie, itp.).

Aby uzyskać więcej informacji na temat działania, zobacz [działania Spark](data-factory-spark.md) artykułu.  

## <a name="machine-learning-batch-execution-activity"></a>Działanie wykonywania wsadowego w usłudze Machine Learning
W definicji usługi Azure ML wsadowe wykonywanie działania JSON można określić następujące właściwości. Właściwość typu działania musi być: **AzureMLBatchExecution**. Musisz utworzyć maszyny platformy Azure, najpierw uczenia połączonej usługi i określ nazwę go jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania AzureMLBatchExecution:

Właściwość | Opis | Wymagane 
-------- | ----------- | --------
webServiceInput | Zestaw danych do przekazania jako dane wejściowe dla usługi sieci web uczenie Maszynowe Azure. Ten zestaw danych, również musi być uwzględniona w danych wejściowych dla działania. |Użyj WebServiceInputActivity lub webServiceInputs. | 
webServiceInputs | Określ zestawy danych można przekazać jako dane wejściowe dla usługi sieci web uczenie Maszynowe Azure. Jeśli usługa sieci web wymaga wielu danych wejściowych, użyj właściwości webServiceInputs zamiast właściwości WebServiceInputActivity. Zestawy danych, które odwołują się **webServiceInputs** muszą także być ujęte w działaniu **dane wejściowe**. | Użyj WebServiceInputActivity lub webServiceInputs. | 
webServiceOutputs | Zestawy danych, które są przypisane jako dane wyjściowe dla usługi sieci web uczenie Maszynowe Azure. Usługa sieci web zwraca dane wyjściowe w tym zestawie danych. | Yes | 
globalParameters | Określ wartości dla parametrów usługi sieci web w tej sekcji. | Nie | 

### <a name="json-example"></a>Przykład JSON
W tym przykładzie działanie ma zestaw danych **MLSqlInput** jako dane wejściowe i **MLSqlOutput** jako dane wyjściowe. **MLSqlInput** przekazany jako dane wejściowe z usługą sieci web przez przy użyciu **WebServiceInputActivity** właściwości JSON. **MLSqlOutput** jest przekazywany jako dane wyjściowe z usługą sieci Web przez przy użyciu **webServiceOutputs** właściwości JSON. 

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }              
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

W przykładzie JSON wdrożonej usługi sieci Web Azure Machine Learning używa czytnika i modułu zapisywania danych bazy danych SQL Azure z/do odczytu/zapisu. Ta usługa sieci Web udostępnia następujące cztery parametry: Nazwa serwera, nazwa bazy danych, nazwę konta użytkownika serwera i hasło konta użytkownika serwera bazy danych.

> [!NOTE]
> Tylko danych wejściowych i wyjściowych działania AzureMLBatchExecution mogą być przekazywane jako parametry z usługą sieci Web. Na przykład w powyższym fragment kodu JSON MLSqlInput jest wartością wejściową działania AzureMLBatchExecution, który jest przekazywany jako dane wejściowe do usługi sieci Web za pomocą parametru WebServiceInputActivity.

## <a name="machine-learning-update-resource-activity"></a>Działanie aktualizowania zasobów w usłudze Machine Learning
W definicji usługi Azure ML aktualizacji zasobów działania JSON można określić następujące właściwości. Właściwość typu działania musi być: **AzureMLUpdateResource**. Musisz utworzyć maszyny platformy Azure, najpierw uczenia połączonej usługi i określ nazwę go jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania AzureMLUpdateResource:

Właściwość | Opis | Wymagane 
-------- | ----------- | --------
trainedModelName | Nazwa modelu retrained. | Yes |  
trainedModelDatasetName | Zestaw danych, wskazując plik iLearner zwrócony przez operację ponownego trenowania. | Yes | 

### <a name="json-example"></a>Przykład JSON
Potok zawiera dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego usługi uczenie Maszynowe Azure przyjmuje jako dane wejściowe dane szkoleniowe i tworzy plik iLearner jako dane wyjściowe. Działanie wywołuje usługę sieci web szkolenia (eksperyment uczenia udostępniony jako usługa sieci web) przy użyciu danych wejściowych szkolenia i odbiera plik ilearner z usługi sieci Web. PlaceholderBlob jest tylko fikcyjny wyjściowego zestawu danych, który jest wymagany przez usługi fabryka danych Azure do uruchamiania potoku.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Można określić następujące właściwości w definicji JSON działanie U-SQL. Właściwość typu działania musi być: **DataLakeAnalyticsU SQL**. Musisz utworzyć usługi Azure Data Lake Analytics połączone i określ nazwę go jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania DataLakeAnalyticsU SQL: 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| scriptPath |Ścieżka do folderu, który zawiera skrypt U-SQL. Nazwa pliku jest rozróżniana wielkość liter. |Nie (Jeśli używasz skryptu) |
| scriptLinkedService |Połączonej usługi, która łączy magazynu, który zawiera skrypt do fabryki danych |Nie (Jeśli używasz skryptu) |
| skrypt |Określ skrypt wbudowany zamiast określania scriptPath i scriptLinkedService. Na przykład: "skrypt": "Test Utwórz bazę danych". |Nie (Jeśli używasz scriptPath i scriptLinkedService) |
| degreeOfParallelism |Maksymalna liczba węzłów jednocześnie użyta do uruchomienia zadania. |Nie |
| priorytet |Określa, które spośród wszystkich znajdujących się w kolejce zadań należy wybrać ma być uruchomiony. Im niższy numer, tym wyższy priorytet. |Nie |
| parameters |Parametry skryptu U-SQL |Nie |

### <a name="json-example"></a>Przykład JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Aby uzyskać więcej informacji, zobacz [Data Lake Analytics U-SQL działania](data-factory-usql-activity.md). 

## <a name="stored-procedure-activity"></a>Działania procedur składowanych
W definicji przechowywane procedury działania JSON można określić następujące właściwości. Właściwość typu działania musi być: **SqlServerStoredProcedure**. Należy utworzyć następujące połączone usługi i określ nazwę połączonej usługi jako wartość **linkedServiceName** właściwości:

- Oprogramowanie SQL Server 
- Azure SQL Database
- Azure SQL Data Warehouse

Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania SqlServerStoredProcedure:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| storedProcedureName |Określ nazwę procedury składowanej w bazie danych Azure SQL lub usługi Azure SQL Data Warehouse jest reprezentowana przez połączonej usługi, która używa tabeli wyników. |Yes |
| storedProcedureParameters |Określ wartości dla parametrów procedury składowanej. Jeśli chcesz przekazać wartości null dla parametru należy użyć składni: "param1": wartość null (małe litery). Zobacz poniższy przykład, aby dowiedzieć się więcej o korzystaniu z tej właściwości. |Nie |

Jeśli określisz wejściowy zestaw danych, musi być dostępny (w stanie "Gotowe") dla działania procedury składowanej do uruchomienia. Wejściowy zestaw danych nie mogą być używane w procedurze składowanej jako parametr. Tylko służy do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. Należy określić zestaw danych wyjściowych dla działania procedury składowanej. 

Określa wyjściowy zestaw danych **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc, itp.). Wyjściowy zestaw danych musi używać **połączona usługa** odwołujący się do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse lub bazy danych programu SQL Server w której ma zostać procedurę składowaną, aby uruchomić. Wyjściowy zestaw danych może stanowić sposób przekazać wyników procedury składowanej dla kolejnych przetwarzania przez inne działanie ([łańcucha działania](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) w potoku. Jednak fabryki danych nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego elementu dataset. Jest procedury przechowywanej, która zapisuje do tabeli SQL, która wskazuje wyjściowego zestawu danych. W niektórych przypadkach może być wyjściowy zestaw danych **fikcyjny zestawu danych**, które służą tylko do określ harmonogram uruchamiania działania procedury składowanej.  

### <a name="json-example"></a>Przykład JSON

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
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Aby uzyskać więcej informacji, zobacz [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) artykułu. 

## <a name="net-custom-activity"></a>Niestandardowe działanie platformy .NET
Można określić następujące właściwości w .NET działań niestandardowych definicji JSON. Właściwość typu działania musi być: **DotNetActivity**. Należy utworzyć usługi Azure HDInsight połączone lub partii zadań Azure połączone usługi i określ nazwę połączonej usługi jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania DotNetActivity:
 
| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| AssemblyName | Nazwa zestawu. W tym przykładzie jest: **MyDotnetActivity.dll**. | Yes |
| EntryPoint |Nazwa klasy, która implementuje interfejs IDotNetActivity. W tym przykładzie jest: **MyDotNetActivityNS.MyDotNetActivity** gdzie przestrzeni nazw jest MyDotNetActivityNS i MyDotNetActivity jest klasą.  | Yes | 
| PackageLinkedService | Nazwa połączoną usługą magazynu Azure wskazujące do magazynu obiektów blob, który zawiera plik zip działania niestandardowego. W tym przykładzie jest: **AzureStorageLinkedService**.| Yes |
| PackageFile | Nazwa pliku zip. W tym przykładzie jest: **customactivitycontainer/MyDotNetActivity.zip**. | Yes |
| właściwości rozszerzone | Rozszerzone właściwości, które można definiować i przekazać kodu platformy .NET. W tym przykładzie **SliceStart** zmienna jest ustawiona wartość opartą na SliceStart zmiennej systemowej. | Nie | 

### <a name="json-example"></a>Przykład JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Aby uzyskać szczegółowe informacje, zobacz [skorzystać z działań niestandardowych w fabryce danych](data-factory-use-custom-activities.md) artykułu. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące samouczki: 

- [Samouczek: tworzenie potoku z działanie kopiowania](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Samouczek: tworzenie potoku z działaniem gałęzi](data-factory-build-your-first-pipeline-using-editor.md)
