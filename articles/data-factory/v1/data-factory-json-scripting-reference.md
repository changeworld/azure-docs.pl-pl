---
title: Usługi Azure Data Factory — Dokumentacja skryptów JSON | Dokumentacja firmy Microsoft
description: Udostępnia schematów JSON dla jednostek usługi Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 25cf9c3b7968be16dcc22f4140725efc22d785f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66156534"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Usługi Azure Data Factory — Dokumentacja skryptów JSON
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory.


Ten artykuł zawiera przykłady i schematy JSON do definiowania jednostek usługi Azure Data Factory (potok, działania, zestaw danych i połączonej usługi).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Potok
Struktury ogólne definicji potoku jest następująca:

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

Poniższa tabela zawiera opis właściwości w definicji JSON potok:

| Właściwość | Opis | Wymagane
-------- | ----------- | --------
| name | Nazwa potoku. Określ nazwę, która reprezentuje akcję działania lub potoku zostanie skonfigurowana w tym<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się literą, cyfrą lub znakiem podkreślenia (\_)</li><li>Nie może zawierać następujących znaków: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Yes |
| description |Tekst opisujący działanie lub potoku do czego służy | Nie |
| activities | Zawiera listę działań. | Tak |
| start |Data i godzina rozpoczęcia dla potoku. Musi znajdować się w [ISO format](https://en.wikipedia.org/wiki/ISO_8601). Na przykład: 2014-10-14T16:32:41. <br/><br/>Istnieje możliwość określenia czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00**-05:00`, które jest szacowane AM 6<br/><br/>Właściwości początkowe i końcowe razem określają aktywny okres potoku. Wycinki danych wyjściowych tylko są tworzone za pomocą w tym okresie active. |Nie<br/><br/>Jeśli określisz wartości dla właściwości end, należy określić wartość dla właściwości rozpoczęcia.<br/><br/>Czas rozpoczęcia i zakończenia zarówno można pozostawić puste, aby utworzyć potok. Należy określić zarówno wartości można ustawić okresu aktywności potoku do uruchomienia. Jeśli nie określono godziny rozpoczęcia i zakończenia podczas tworzenia potoku, można ustawić je później przy użyciu polecenia cmdlet Set-AzDataFactoryPipelineActivePeriod. |
| end |Data / Godzina zakończenia dla potoku. Jeśli zostanie określony, musi być w formacie ISO. Na przykład: 2014-10-14T17:32:41 <br/><br/>Istnieje możliwość określenia czasu lokalnego, na przykład czasu EST. Oto przykład: `2016-02-27T06:00:00**-05:00`, które jest szacowane AM 6<br/><br/>Aby uruchomić potok bezterminowo, określ 9999-09-09 jako wartość właściwości end. |Nie <br/><br/>Jeśli określisz wartości dla właściwości uruchamiania, należy określić wartość dla właściwości end.<br/><br/>Zobacz informacje o **start** właściwości. |
| isPaused |Jeśli ustawiono wartość true, potok nie jest uruchamiany. Wartość domyślna = false. Ta właściwość służy do włączania lub wyłączania. |Nie |
| pipelineMode |Metoda Planowanie uruchomienia potoku. Dozwolone wartości to: (ustawienie domyślne), zaplanowane jednorazowa.<br/><br/>"Regularne" wskazuje, że potok jest uruchamiany w określonych odstępach czasu zgodnie z jego aktywny okres (czas rozpoczęcia i zakończenia). "Jednorazowe" wskazuje, że potok jest uruchamiany tylko raz. Jednorazowa potoki po utworzeniu nie może być zmodyfikowane/zaktualizowane obecnie. Zobacz [potoku Onetime](data-factory-create-pipelines.md#onetime-pipeline) Aby uzyskać szczegółowe informacje o ustawieniach jednorazowa. |Nie |
| expirationTime |Czas, po utworzeniu, dla którego potok jest prawidłowa i powinny pozostać elastycznie. Jeśli nie ma żadnych aktywnych, nie powiodło się, lub do czasu uruchomienia potoku jest automatycznie usuwany po ogłoszeniu czas wygaśnięcia. |Nie |


## <a name="activity"></a>Działanie
Ogólną strukturę dla działania w ramach definicji potoku (element działania), jest następujący:

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
    },
    "scheduler":
    {
    }
}
```

Następujących tabeli opisano właściwości w definicji JSON działania:

| Tag | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa działania. Określ nazwę, która reprezentuje akcję skonfigurowanego działania<br/><ul><li>Maksymalna liczba znaków: 260</li><li>Musi zaczynać się literą, cyfrą lub znakiem podkreślenia (\_)</li><li>Nie może zawierać następujących znaków: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Yes |
| description |Tekst opisujący przeznaczenie działania. |Nie |
| type |Określa typ działania. Zobacz [MAGAZYNY danych](#data-stores) i [działania PRZEKSZTAŁCANIA danych](#data-transformation-activities) sekcje dla różnych typów działań. |Tak |
| dane wejściowe |Tabele wejściowe używane przez działanie<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Brak działań HDInsightStreaming i SqlServerStoredProcedure <br/> <br/> Tak dla wszystkich innych |
| outputs |Dane wyjściowe tabele używane przez działanie.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Yes |
| linkedServiceName |Nazwa połączonej usługi używana na potrzeby działania. <br/><br/>Działanie może wymagać określenia połączonej usługi, która stanowi łącze do wymaganego środowiska obliczeniowego. |Tak dla działań HDInsight, Azure Machine Learning działań i działania dotyczącego procedury składowanej. <br/><br/>Nie dla wszystkich innych |
| typeProperties |Właściwości w sekcji typeProperties zależą od typu działania. |Nie |
| policy |Zasady, które mają wpływ na zachowanie działania w czasie wykonania. Jeśli nie jest określona, używane są domyślne zasady. |Nie |
| Harmonogram |Właściwość "harmonogram" jest używana do definiowania żądanego planowania dla działania. Właściwości podrzędnych są takie same, jak te w [właściwości availability w zestawie danych](data-factory-create-datasets.md#dataset-availability). |Nie |

### <a name="policies"></a>Zasady
Zasady wpływają na zachowania w czasie wykonywania działania, w szczególności, po przetworzeniu wycinka tabeli. Poniższa tabela zawiera szczegółowe informacje.

| Właściwość | Dozwolone wartości | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| concurrency |Liczba całkowita <br/><br/>Wartość maksymalna: 10 |1 |Liczba współbieżnych wykonań działania.<br/><br/>Określa liczbę wykonań działania równoległego, które mogą być uruchomione na różnych wycinki. Na przykład jeśli działanie musi przechodzić przez duży zestaw dostępnych danych, o wartości większej współbieżności przyspiesza przetwarzanie danych. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Określa kolejność wycinki danych, które są przetwarzane.<br/><br/>Na przykład jeśli masz 2 dzieli (jeden występuje o 16: 00 i inną o 17: 00), a oba są oczekiwanie na wykonanie. Jeśli ustawisz executionPriorityOrder jako NewestFirst, jest przetwarzana najpierw wycinek o 17: 00. Podobnie jeśli ustawisz executionPriorityORder jako OldestFIrst, następnie wycinka u 16: 00 jest przetwarzany. |
| retry |Liczba całkowita<br/><br/>Maksymalna wartość może wynosić 10 |0 |Liczba ponownych prób zanim przetwarzania danych dla wycinka jest oznaczony jako niepowodzenie. Wykonania działania dla wycinka danych zostanie ponowiony do określonej liczby ponownych prób. Ponowienie próby jest wykonywane tak szybko, jak to możliwe po niepowodzeniu. |
| timeout |TimeSpan |00:00:00 |Limit czasu działania. Przykład: 00:10:00 (oznacza limit czasu 10 minut)<br/><br/>Jeśli wartość nie została określona lub ma wartość 0, limit czasu jest nieskończona.<br/><br/>Jeśli czas przetwarzania danych na wycinek przekracza wartość limitu czasu, zostanie anulowane, a system podejmuje próbę przetwarzania. Liczba ponownych prób, zależy od właściwości ponownych prób. W przypadku przekroczenia limitu czasu stan jest ustawiony na przekroczenie limitu czasu. |
| delay |TimeSpan |00:00:00 |Określ opóźnienie przed rozpoczęciem przetwarzania danych startów wycinka.<br/><br/>Wykonywanie działania dla wycinka danych została uruchomiona po oczekiwanym czasie wykonywania opóźnienie.<br/><br/>Przykład: 00:10:00 (implikuje użycie opóźnieniem 10 minut) |
| longRetry |Liczba całkowita<br/><br/>Wartość maksymalna: 10 |1 |Liczba prób długa — ponowienie próby, zanim wycinek wykonanie nie powiodło się.<br/><br/>są rozciągane w prób longRetry, longRetryInterval. Więc jeśli potrzebujesz określić czas między ponownymi próbami, należy użyć longRetry. Jeśli określono zarówno longRetry, jak i ponów próbę kolejnymi próbami longRetry zawiera ponownymi próbami i maksymalną liczbę prób ponawiania * longRetry.<br/><br/>Na przykład, mamy następujące ustawienia zasad dotyczących działań:<br/>Ponów próbę: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Przyjęto założenie, istnieje tylko jeden wycinek do wykonania (oczekiwanie stanu) i wykonania działania każdym razem, gdy kończy się niepowodzeniem. Początkowo będzie można 3 próby wykonania kolejnych. Po każdej próbie stan wycinka byłoby ponownych prób. Po pierwsze 3 prób przez, stan wycinka byłoby LongRetry.<br/><br/>Po upływie godziny (czyli wartość longRetryInteval firmy) będzie inny zbiór 3 próby wykonania kolejnych. Po tym stan wycinka, czy nie, a wszystkie próby może nastąpić. Dlatego całkowity podejmowano próby 6.<br/><br/>Jeśli wykonanie dowolnego zakończy się powodzeniem, stan wycinka będzie gotowy, a wszystkie próby są próby.<br/><br/>longRetry mogą być używane w sytuacji, w którym dane zależne dociera niedeterministyczne razy lub niestabilnym całego środowiska, w ramach której przetwarzania danych. W takich przypadkach to ponownych prób po kolei może nie pozwalających i sposób po upływie czasu skutkuje żądaną produktu wyjściowego.<br/><br/>Word Przestroga: nie należy ustawiać wysokiej wartości longRetry lub longRetryInterval. Zazwyczaj wyższe wartości oznaczają innych kwestii systemowych. |
| longRetryInterval |TimeSpan |00:00:00 |Opóźnienie między próbami długa — ponowienie próby |

### <a name="typeproperties-section"></a>sekcji typeProperties
Dla każdego działania różni się w sekcji typeProperties. Działania przekształcania mają tylko właściwości typu. Zobacz [działania PRZEKSZTAŁCANIA danych](#data-transformation-activities) sekcję w tym artykule, aby uzyskać przykłady kodu JSON, definiujące działań przekształcania w potoku.

**Działanie kopiowania, które** ma dwa podsekcje w sekcji typeProperties: **źródła** i **ujścia**. Zobacz [MAGAZYNY danych](#data-stores) sekcji w niniejszym artykule przykłady kodu JSON, pokazujące, jak za pomocą danych przechowywane jako źródła lub ujścia.

### <a name="sample-copy-pipeline"></a>Przykładowy potok kopiowania
W poniższym przykładowym potoku występuje jedno działanie typu **Copy** w sekcji **activities**. W tym przykładzie [działanie kopiowania, które](data-factory-data-movement-activities.md) kopiuje dane z usługi Azure Blob storage do usługi Azure SQL database.

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

Zobacz [MAGAZYNY danych](#data-stores) sekcji w niniejszym artykule przykłady kodu JSON, pokazujące, jak za pomocą danych przechowywane jako źródła lub ujścia.

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [samouczka: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: kopiowanie danych z usługi Blob Storage do usługi SQL Database).

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
* **Definiuje** sekcja jest używana do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu hive jako wartości konfiguracyjne magazynu Hive (np. `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Zobacz [działania PRZEKSZTAŁCANIA danych](#data-transformation-activities) sekcję w tym artykule, aby uzyskać przykłady kodu JSON, definiujące działań przekształcania w potoku.

Aby uzyskać szczegółowy przewodnik tworzenia tego potoku, zobacz [samouczka: Tworzenie pierwszego potoku do przetwarzania danych przy użyciu klastra Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Połączona usługa
Ogólną strukturę dla definicji usługi połączonej jest następująca:

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

Następujących tabeli opisano właściwości w definicji JSON działania:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| name | Nazwa połączonej usługi. | Tak |
| właściwości — Typ | Typ połączonej usługi. Na przykład: Usługa Azure Storage, usługi Azure SQL Database. |
| typeProperties | W sekcji typeProperties ma elementy, które są różne dla każdego magazynu danych lub środowisko obliczeniowe. Zobacz sekcję magazynów danych, dla wszystkich danych, połączonych usług magazynu i [środowisk obliczeniowych](#compute-environments) wszystkie zasoby obliczeniowe usługi połączonej |

## <a name="dataset"></a>Zestaw danych
Zestaw danych w usłudze Azure Data Factory jest zdefiniowana w następujący sposób:

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

W poniższej tabeli opisano właściwości w powyższy kod JSON:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| name | Nazwa zestawu danych. Zobacz [usługi Azure Data Factory — reguły nazewnictwa](data-factory-naming-rules.md) reguły nazewnictwa. |Tak |Nie dotyczy |
| type | Typ zestawu danych. Określ jeden z typów obsługiwanych przez usługę Azure Data Factory (na przykład: AzureBlob, AzureSqlTable). Zobacz [MAGAZYNY danych](#data-stores) sekcji dla wszystkich magazynów danych i typy zestawów danych obsługiwane przez usługę Data Factory. |
| Struktura | Schemat zestawu danych. Zawiera on kolumny, jak ich typy, itp. | Nie |Nie dotyczy |
| typeProperties | Właściwości odpowiadający wybranego typu. Zobacz [MAGAZYNY danych](#data-stores) dotyczącej obsługiwanych typów i ich właściwości. |Tak |Nie dotyczy |
| external | Flagę logiczną, aby określić, czy zestaw danych jest jawnie generowany przez potok usługi data factory, czy nie. |Nie |false |
| availability | Definiuje okno przetwarzania lub model tworzenia wycinków w środowisku produkcyjnym zestaw danych. Szczegółowe informacje na temat zestawu danych model tworzenia wycinków, [planowanie i wykonywanie](data-factory-scheduling-and-execution.md) artykułu. |Tak |Nie dotyczy |
| policy |Definiuje kryteria lub warunek, który należy spełnić wycinków zestawu danych. <br/><br/>Aby uzyskać szczegółowe informacje, zobacz zasady Dataset sekcji. |Nie |Nie dotyczy |

Każda kolumna w **struktury** sekcja zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| name |Nazwa kolumny. |Tak |
| type |Typ danych kolumny.  |Nie |
| culture |Kultura ma być używany, gdy typ jest określony, a typ architektury .NET oparte na platformie .NET `Datetime` lub `Datetimeoffset`. Wartość domyślna to `en-us`. |Nie |
| format |Format ciągu ma być używany, gdy typ jest określony, a typ architektury .NET `Datetime` lub `Datetimeoffset`. |Nie |

W poniższym przykładzie zestaw danych zawiera trzy kolumny `slicetimestamp`, `projectname`, i `pageviews` i typu: Ciąg, ciąg i dziesiętnych odpowiednio.

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
| frequency |Określa jednostkę czasu dla trybu produkcyjnego wycinek zestawu danych.<br/><br/><b>Obsługiwana częstotliwość</b>: Minuty, godziny, dnia, tygodnia, miesiąca |Tak |Nie dotyczy |
| interval |Określa mnożnik częstotliwości<br/><br/>"Interwał częstotliwości x" Określa, jak często wycinek jest generowany.<br/><br/>Zestaw danych można podzielić w systemie godzinowym, należy ustawić <b>częstotliwość</b> do <b>godzinę</b>, i <b>interwał</b> do <b>1</b>.<br/><br/><b>Uwaga</b>: Jeśli określisz częstotliwość co minutę, zaleca się ustawić interwał wynoszący nie mniej niż 15 |Tak |Nie dotyczy |
| Styl |Określa, czy wycinek powinny być tworzone na początku/końca zakresu.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Jeśli parametr Frequency ma wartość miesiąca i styl jest równa EndOfInterval, wycinek jest generowany na ostatni dzień miesiąca. Jeśli styl StartOfInterval wycinek jest generowany pierwszego dnia miesiąca.<br/><br/>Jeśli parametr Frequency ma wartość dnia i styl jest równa EndOfInterval, wycinek jest generowany w ciągu ostatniej godziny, dnia.<br/><br/>Jeśli ustawiono styl EndOfInterval Frequency ma wartość Hour, wycinek jest generowany na koniec godziny. Na przykład dla wycinka okres 13: 00 – 2 PM, wycinek jest generowany w 14: 00. |Nie |EndOfInterval |
| anchorDateTime |Definiuje położenie bezwzględne w czasie używanych przez harmonogram do obliczenia granice wycinek zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli AnchorDateTime części daty, które są bardziej szczegółowe niż częstotliwość bardziej szczegółowego części są ignorowane. <br/><br/>Na przykład jeśli <b>interwał</b> jest <b>co godzinę</b> (frequency: hour, interval: (1) i <b>AnchorDateTime</b> zawiera <b>minuty i sekundy</b> , a następnie <b>minuty i sekundy</b> części AnchorDateTime są ignorowane. |Nie |01/01/0001 |
| offset |Zakres czasu za pomocą którego przesunięte początek i koniec okresu wszystkich wycinków zestawu danych. <br/><br/><b>Uwaga</b>: Jeśli określono zarówno anchorDateTime, jak i przesunięcie, wynik jest połączone shift. |Nie |Nie dotyczy |

W poniższej sekcji dostępności Określa, że wyjściowy zestaw danych jest utworzone co godzinę (lub) danych wejściowych co godzinę zestaw danych jest dostępny:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

**Zasad** sekcji w definicji zestawu danych definiuje kryteria lub warunek, który należy spełnić wycinków zestawu danych.

| Nazwa zasad | Opis | Dotyczy | Wymagane | Domyślne |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Sprawdza, czy dane w **obiektów blob platformy Azure** spełnia wymagania minimalny rozmiar (w megabajtach). |Obiekt blob platformy Azure |Nie |Nie dotyczy |
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

Chyba, że zestaw danych jest generowany przez usługę Azure Data Factory, powinien być oznaczony jako **zewnętrznych**. To ustawienie dotyczy dane wejściowe pierwszego działania w potoku, chyba że działania lub łańcucha potoku jest używany.

| Name (Nazwa) | Opis | Wymagane | Wartość domyślna |
| --- | --- | --- | --- |
| dataDelay |Czas opóźnienia wyboru na dostępność danych zewnętrznych dla danego wycinka. Na przykład dane są dostępne co godzinę, może być opóźniony przy użyciu dataDelay wyboru do danych zewnętrznych jest dostępna i odpowiednie wycinek jest gotowy.<br/><br/>Ma zastosowanie tylko do chwili obecnej.  Na przykład jeśli jest 1:00 PM teraz, a wartość ta wynosi 10 minut, sprawdzanie poprawności rozpoczyna się od 1:10 PM.<br/><br/>To ustawienie nie wpływa na wycinki w przeszłości (wycinków z czas zakończenia wycinka + dataDelay < teraz) są przetwarzane bez żadnego opóźnienia.<br/><br/>Czasu większą niż 23:59 godziny należy określać z użyciem `day.hours:minutes:seconds` formatu. Na przykład aby określić 24 godziny, nie używaj 24:00:00; Zamiast tego należy użyć 1.00:00:00. Jeśli używasz 24:00:00, jest ona traktowana jako 24 dni (24.00:00:00). W przypadku 1 dzień i 4 godziny należy określić 1:04:00:00. |Nie |0 |
| retryInterval |Czas oczekiwania między awarii, a następnie ponów próbę. W przypadku niepowodzenia try następnej próbie jest po retryInterval. <br/><br/>Jeśli jest to 1:00 PM teraz, możemy rozpocząć pierwszej próby. W przypadku 1 minutę i operacja nie powiodła się czas do ukończenia pierwsze sprawdzenie poprawności następne ponowienie próby wynosi 1:00 + 1 min (czas trwania) + 1 min (interwał ponawiania) = 13:02:00. <br/><br/>Wycinki w przeszłości nie ma żadnego opóźnienia. Ponowienie próby odbywa się natychmiast. |Nie |00:01:00 (1 minuta) |
| retryTimeout |Limit czasu dla każdego ponowienia próby.<br/><br/>Jeśli ta właściwość jest ustawiona na 10 minut, sprawdzanie poprawności musi zostać wykonane w ciągu 10 minut. Jeśli trwa dłużej niż 10 minut, aby wykonać sprawdzanie poprawności, ponowienie próby upłynie limit czasu.<br/><br/>Jeśli upłynie limit czasu wszystkich prób weryfikacji, wycinek zostanie oznaczona jako przekroczenie limitu czasu. |Nie |00:10:00 (10 minut) |
| maximumRetry |Liczba przypadków, aby sprawdzić dostępność danych zewnętrznych. Wartość maksymalna dozwolona wynosi 10. |Nie |3 |


## <a name="data-stores"></a>MAGAZYNY DANYCH
[Połączoną usługę](#linked-service) sekcji określone opisy elementów JSON, które są wspólne dla wszystkich typów połączonych usług. Ta sekcja zawiera szczegółowe informacje o elementach JSON, które są specyficzne dla każdego magazynu danych.

[Dataset](#dataset) sekcji określone opisy elementów JSON, które są wspólne dla wszystkich typów zestawów danych. Ta sekcja zawiera szczegółowe informacje o elementach JSON, które są specyficzne dla każdego magazynu danych.

[Działania](#activity) sekcji określone opisy elementów JSON, które są wspólne dla wszystkich typów działań. Ta sekcja zawiera szczegółowe informacje o elementach JSON, które są specyficzne dla każdego magazynu danych, gdy jest ona używana jako źródła/ujścia w działaniu kopiowania.

Kliknij link dla magazynu, który chcesz wyświetlić schematów JSON dla połączonej usługi, zestaw danych i źródła/ujścia dla działania kopiowania.

| Category | Magazyn danych
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
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
| &nbsp; |Tabela internetowa |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Połączona usługa
Istnieją dwa typy połączonych usług: Połączona usługa Azure Storage i połączone usługi Azure Storage sygnatury dostępu Współdzielonego usługi.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć konto usługi Azure storage z fabryką danych przy użyciu **klucz konta**, Utwórz połączoną usługę Azure Storage. Do usługi Azure Storage do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureStorage**. Następnie można określić następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| connectionString |Określ informacje potrzebne do łączenia z usługą Azure storage dla właściwości connectionString. |Tak |

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
Usługa połączona sygnatury dostępu Współdzielonego Azure Storage pozwala połączyć konto usługi Azure Storage do usługi Azure data factory przy użyciu sygnatury dostępu współdzielonego (SAS). Usługi data factory zapewnia ograniczony/czasowo dostęp do wszystkich/dla zasobów (obiekt blob/kontenera) w magazynie. Aby połączyć konto usługi Azure storage z fabryką danych przy użyciu sygnatura dostępu współdzielonego, tworzenie sygnatury dostępu Współdzielonego Azure Storage połączoną usługę. Aby zdefiniować sygnatury dostępu Współdzielonego Azure Storage połączoną usługę, należy ustawić **typu** połączonej usługi, aby **AzureStorageSas**. Następnie można określić następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| sasUri |Określ udostępniony URI podpisu dostępu do zasobów usługi Azure Storage, np. obiektów blob, kontenerów lub tabeli. |Tak |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz [łącznika usługi Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych obiektów Blob platformy Azure, ustaw **typu** zestawu danych na **AzureBlob**. Następnie określ następujące właściwości określonego obiektu Blob platformy Azure w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do kontenera i folderu w magazynie obiektów blob. Przykład: myblobcontainer\myblobfolder\ |Tak |
| fileName |Nazwa obiektu blob. Nazwa pliku jest opcjonalny i wielkość liter.<br/><br/>Jeśli określisz parametr filename, aktywności (w tym kopiowania) działa na konkretny obiekt Blob.<br/><br/>Jeśli nie określono nazwy pliku, kopiowania obejmuje wszystkie obiekty BLOB w ścieżce folderu dla wejściowego zestawu danych.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku byłoby w następującym następujący format: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest opcjonalną właściwością. Służy do określania folderPath dynamiczne i nazwę pliku do danych szeregów czasowych. Na przykład folderPath mogą być parametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

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

### <a name="blobsource-in-copy-activity"></a>BlobSource w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Blob Storage, ustaw **typ źródła** z działaniem kopiowania, aby **BlobSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. |TRUE, False (wartość domyślna) |Nie |

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
### <a name="blobsink-in-copy-activity"></a>BlobSink w działaniu kopiowania
Jeśli dane są kopiowane do usługi Azure Blob Storage, ustaw **typ ujścia** z działaniem kopiowania, aby **BlobSink**i określ następujące właściwości w **ujścia** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest BlobSource lub systemu plików. |<b>PreserveHierarchy</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego znajdują się w pierwszy poziom folderu docelowego. Pliki docelowe mają automatycznie wygeneruje nazwę. <br/><br/><b>MergeFiles (ustawienie domyślne):</b> scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu Blob jest określony, nazwa pliku scalonego będzie określoną nazwą; w przeciwnym razie może być nazwą pliku generowanych automatycznie. |Nie |

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
Do usługi Azure Data Lake Store definiowania połączonej usługi, należy ustawić typ połączonej usługi, aby **AzureDataLakeStore**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **AzureDataLakeStore** | Tak |
| dataLakeStoreUri | Określ informacje o koncie usługi Azure Data Lake Store. W następującym formacie: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Tak |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do której należy Data Lake Store. | Wymagane dla ujścia |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy Data Lake Store. | Wymagane dla ujścia |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak (w przypadku uwierzytelniania jednostki usługi) |
| servicePrincipalKey | Określ klucz aplikacji. | Tak (w przypadku uwierzytelniania jednostki usługi) |
| tenant | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure Portal. | Tak (w przypadku uwierzytelniania jednostki usługi) |
| Autoryzacja | Kliknij przycisk **Autoryzuj** znajdujący się w **edytora fabryki danych** i wprowadź swoje poświadczenia, które przypisuje adres URL autoryzacji wygenerowany automatycznie do tej właściwości. | Tak (w przypadku uwierzytelniania poświadczeń użytkownika)|
| sessionId | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używana tylko raz. To ustawienie jest generowany automatycznie, korzystając z edytora fabryki danych. | Tak (w przypadku uwierzytelniania poświadczeń użytkownika) |

#### <a name="example-using-service-principal-authentication"></a>Przykład: przy użyciu uwierzytelniania jednostki usługi
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

#### <a name="example-using-user-credential-authentication"></a>Przykład: użycie uwierzytelniania poświadczeń użytkownika
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
Aby zdefiniować zestaw danych usługi Azure Data Lake Store, ustaw **typu** zestawu danych na **AzureDataLakeStore**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| folderPath |Ścieżka do kontenera i folderu w usłudze Azure Data Lake przechowywać. |Tak |
| fileName |Nazwa pliku w usłudze Azure Data Lake store. Nazwa pliku jest opcjonalny i wielkość liter. <br/><br/>Jeśli określisz parametr filename, aktywności (w tym kopiowania) działa na określonego pliku.<br/><br/>Jeśli nie określono nazwy pliku, kopia uwzględnia wszystkie pliki w ścieżce folderu dla wejściowego zestawu danych.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku byłoby w następującym następujący format: `Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy jest opcjonalną właściwością. Służy do określania folderPath dynamiczne i nazwę pliku do danych szeregów czasowych. Na przykład folderPath mogą być parametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

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

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Źródło usługi Azure Data Lake Store w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Data Lake Store, ustaw **typ źródła** z działaniem kopiowania, aby **AzureDataLakeStoreSource**, a następnie określ następujące właściwości w **źródła**sekcji:

**AzureDataLakeStoreSource** obsługuje następujące właściwości **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. |TRUE, False (wartość domyślna) |Nie |

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

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store ujścia w działaniu kopiowania
Jeśli dane są kopiowane do usługi Azure Data Lake Store, ustaw **typ ujścia** z działaniem kopiowania, aby **AzureDataLakeStoreSink**, a następnie określ następujące właściwości w **ujścia** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone z automatycznie wygeneruje nazwę.<br/><br/><b>MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku/obiektu Blob jest określony, nazwa pliku scalonego będzie określoną nazwą; w przeciwnym razie może być nazwą pliku generowanych automatycznie. |Nie |

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
Do usługi Azure Cosmos DB do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **bazy danych DocumentDb**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| **Property** | **Opis** | **Wymagane** |
| --- | --- | --- |
| connectionString |Określ informacje potrzebne do łączenia z bazą danych Azure Cosmos DB. |Tak |

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
Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi Azure Cosmos DB, ustaw **typu** zestawu danych na **DocumentDbCollection**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| **Property** | **Opis** | **Wymagane** |
| --- | --- | --- |
| collectionName |Nazwa kolekcji usługi Azure Cosmos DB. |Tak |

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
Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) artykułu.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Źródło kolekcji usługi Azure Cosmos DB w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Cosmos DB, ustaw **typ źródła** z działaniem kopiowania, aby **DocumentDbCollectionSource**, a następnie określ następujące właściwości w **źródła**sekcji:


| **Property** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| query |Określ zapytanie, które można odczytać danych. |Zapytanie ciągu obsługiwany przez usługę Azure Cosmos DB. <br/><br/>Przykład: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nie <br/><br/>Jeśli nie zostanie określony, instrukcję SQL, która jest wykonywana: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Znaki specjalne, aby wskazać, że dokument jest zagnieżdżona |Dowolny znak. <br/><br/>Usługa Azure Cosmos DB jest magazyn danych NoSQL dla dokumentów JSON, gdzie struktury zagnieżdżone są dozwolone. Usługa Azure Data Factory umożliwia użytkownikowi oznaczają hierarchii za pomocą nestingSeparator, czyli "." w powyższych przykładach. Separatorem, działanie kopiowania generuje obiekt "Name", z trzema elementami podrzędnymi elementami najpierw drugie imię i nazwisko, zgodnie z "Name.First", "Name.Middle" i "Name.Last" w definicji tabeli. |Nie |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB kolekcji ujścia w działaniu kopiowania
Jeśli dane są kopiowane do usługi Azure Cosmos DB, ustaw **typ ujścia** z działaniem kopiowania, aby **DocumentDbCollectionSink**, a następnie określ następujące właściwości w **ujścia** sekcji :

| **Property** | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| nestingSeparator |Znaki specjalne w nazwa kolumny źródłowej, aby wskazać zagnieżdżonych dokumentu jest wymagana. <br/><br/>Na przykład powyżej: `Name.First` w danych wyjściowych tabeli tworzy następującą strukturę JSON w dokumencie usługi Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Znak używany do rozdzielania poziomów zagnieżdżenia.<br/><br/>Wartość domyślna to `.` (kropka). |Znak używany do rozdzielania poziomów zagnieżdżenia. <br/><br/>Wartość domyślna to `.` (kropka). |
| writeBatchSize |Liczba równoległych żądań do usługi Azure Cosmos DB do tworzenia dokumentów.<br/><br/>Podczas kopiowania danych z usługi Azure Cosmos DB przy użyciu tej właściwości można dostosować wydajność. Lepszą wydajność można oczekiwać, gdy zwiększasz writeBatchSize, ponieważ więcej równoległych żądań do usługi Azure Cosmos DB są wysyłane. Jednak należy unikać ograniczania przepustowości, która może zgłosić komunikat o błędzie: "Liczba żądań jest duży."<br/><br/>Ograniczanie zadecyduje o wiele czynników, w tym rozmiar dokumentów, liczbę warunków w dokumentach, indeksowanie zasady kolekcji docelowej, itd. Dla operacji kopiowania umożliwia lepsze kolekcji (na przykład warstwa S3) najbardziej w dostępne przepływności (2500 żądanie jednostek na sekundę). |Integer |Nie (domyślne: 5) |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji oczekiwania. |TimeSpan<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

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

Aby uzyskać więcej informacji, zobacz [łącznika usługi Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) artykułu.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Połączona usługa
Do usługi Azure SQL Database do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDatabase**, a następnie określ następujące właściwości w **typeProperties** sekcja:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| connectionString |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Database dla właściwości connectionString. |Tak |

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
Aby zdefiniować zestaw danych usługi Azure SQL Database, ustaw **typu** zestawu danych na **AzureSqlTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu usługi Azure SQL Database, która połączona usługa przywołuje. |Tak |

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

### <a name="sql-source-in-copy-activity"></a>Źródło SQL w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure SQL Database, ustaw **typ źródła** z działaniem kopiowania, aby **SqlSource**, a następnie określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

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

### <a name="sql-sink-in-copy-activity"></a>SQL ujścia w działaniu kopiowania
Jeśli dane są kopiowane do usługi Azure SQL Database, ustaw **typ ujścia** z działaniem kopiowania, aby **SqlSink**, a następnie określ następujące właściwości w **ujścia** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |TimeSpan<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania w celu wypełnienia identyfikatorem automatycznie generowane wycinek, który jest używany do czyszczenia danych określonego wycinka czas ponownego. |Nazwa kolumny kolumny z typem danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej danych wykonuje operację UPSERT (aktualizacje/wstawia) do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, w której są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

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
Do usługi Azure SQL Data Warehouse do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDW**, a następnie określ następujące właściwości w **typeProperties** sekcja:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| connectionString |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse dla właściwości connectionString. |Tak |



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
Aby zdefiniować zestaw danych usługi Azure SQL Data Warehouse, ustaw **typu** zestawu danych na **AzureSqlDWTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych Azure SQL Data Warehouse, których połączona usługa przywołuje. |Tak |

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

### <a name="sql-dw-source-in-copy-activity"></a>Źródłowy magazyn danych SQL w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure SQL Data Warehouse, ustaw **typ źródła** z działaniem kopiowania, aby **SqlDWSource**, a następnie określ następujące właściwości w **źródła** sekcja:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

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

### <a name="sql-dw-sink-in-copy-activity"></a>SQL magazynu danych ujścia w działaniu kopiowania
Jeśli dane są kopiowane do usługi Azure SQL Data Warehouse, ustaw **typ ujścia** z działaniem kopiowania, aby **SqlDWSink**i określ następujące właściwości w **ujścia** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. |Instrukcja zapytania. |Nie |
| allowPolyBase |Wskazuje, czy do korzystania z technologii PolyBase (jeśli ma zastosowanie) zamiast mechanizmu BULKINSERT. <br/><br/> **Przy użyciu technologii PolyBase jest zalecany sposób ładowania danych do usługi SQL Data Warehouse.** |True <br/>FALSE (domyślnie) |Nie |
| polyBaseSettings |Grupa właściwości, które może być określony, gdy **allowPolybase** właściwość jest ustawiona na **true**. |&nbsp; |Nie |
| rejectValue |Określa liczbę lub wartość procentowa wierszy, które można odrzucić przed zapytanie nie powiedzie się. <br/><br/>Dowiedz się więcej na temat opcji odrzucania programu PolyBase w **argumenty** części [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tematu. |0 (domyślnie), 1, 2... |Nie |
| rejectType |Określa, czy opcja rejectValue jest określony jako wartość literału lub wartości procentowej. |Wartość (ustawienie domyślne), wartość procentowa |Nie |
| rejectSampleValue |Określa liczbę wierszy do pobrania przed programu PolyBase ponownie oblicza odsetek odrzuconych wierszy. |1, 2, … |Tak, jeśli **rejectType** jest **procent** |
| useTypeDefault |Określa sposób obsługi brakujących wartości w rozdzielanych plików tekstowych, jeśli funkcja PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej na temat tej właściwości z sekcji argumentów w [tworzenie EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Wartość true, False (domyślnie) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |TimeSpan<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |

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
Do usługi Azure Search do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSearch**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| url | Adres URL dla usługi Azure Search. | Yes |
| key | Klucz administratora dla usługi Azure Search. | Tak |

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
Aby zdefiniować zestaw danych usługi Azure Search, ustaw **typu** zestawu danych na **AzureSearchIndex**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| type | Właściwość type musi być równa **AzureSearchIndex**.| Tak |
| indexName | Nazwa indeksu usługi Azure Search. Fabryki danych nie powoduje utworzenia indeksu. Indeks musi istnieć w usłudze Azure Search. | Tak |

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

### <a name="azure-search-index-sink-in-copy-activity"></a>Usługa Azure Search Index ujścia w działaniu kopiowania
Jeśli dane są kopiowane do indeksu usługi Azure Search, ustaw **typ ujścia** z działaniem kopiowania, aby **AzureSearchIndexSink**i określ następujące właściwości w **ujścia** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Określa, czy należy scalić lub Zastąp, jeśli istnieje już dokument w indeksie. | Scal (ustawienie domyślne)<br/>Przekazanie| Nie |
| WriteBatchSize | Przekazywanie danych do indeksu usługi Azure Search, gdy writeBatchSize osiągnie rozmiar buforu. | od 1 do 1000. Wartość domyślna to 1000. | Nie |

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
Istnieją dwa typy połączonych usług: Połączona usługa Azure Storage i połączone usługi Azure Storage sygnatury dostępu Współdzielonego usługi.

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Aby połączyć konto usługi Azure storage z fabryką danych przy użyciu **klucz konta**, Utwórz połączoną usługę Azure Storage. Do usługi Azure Storage do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureStorage**. Następnie można określić następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa: **AzureStorage** |Tak |
| connectionString |Określ informacje potrzebne do łączenia z usługą Azure storage dla właściwości connectionString. |Tak |

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
Usługa połączona sygnatury dostępu Współdzielonego Azure Storage pozwala połączyć konto usługi Azure Storage do usługi Azure data factory przy użyciu sygnatury dostępu współdzielonego (SAS). Usługi data factory zapewnia ograniczony/czasowo dostęp do wszystkich/dla zasobów (obiekt blob/kontenera) w magazynie. Aby połączyć konto usługi Azure storage z fabryką danych przy użyciu sygnatura dostępu współdzielonego, tworzenie sygnatury dostępu Współdzielonego Azure Storage połączoną usługę. Aby zdefiniować sygnatury dostępu Współdzielonego Azure Storage połączoną usługę, należy ustawić **typu** połączonej usługi, aby **AzureStorageSas**. Następnie można określić następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa: **AzureStorageSas** |Tak |
| sasUri |Określ udostępniony URI podpisu dostępu do zasobów usługi Azure Storage, np. obiektów blob, kontenerów lub tabeli. |Tak |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi Azure Table, ustaw **typu** zestawu danych na **AzureTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych tabel platformy Azure, która połączona usługa przywołuje. |Tak. Gdy właściwość tableName jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli podano także azureTableSourceQuery, rekordy z tabeli, która spełnia warunki zapytania są kopiowane do lokalizacji docelowej. |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#dataset-properties) artykułu.

### <a name="azure-table-source-in-copy-activity"></a>Źródło tabeli platformy Azure w działaniu kopiowania
Jeśli kopiujesz dane z usługi Azure Table Storage, ustaw **typ źródła** z działaniem kopiowania, aby **AzureTableSource**, a następnie określ następujące właściwości w **źródła** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania tabeli platformy Azure. Zobacz przykłady w następnej sekcji. |Nie. Gdy właściwość tableName jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do miejsca docelowego. Jeśli podano także azureTableSourceQuery, rekordy z tabeli, która spełnia warunki zapytania są kopiowane do lokalizacji docelowej. |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy wchłonąć wyjątek tabela nie istnieje. |WARTOŚĆ TRUE<br/>WARTOŚĆ FALSE |Nie |

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

Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) artykułu.

### <a name="azure-table-sink-in-copy-activity"></a>Tabela platformy Azure ujścia w działaniu kopiowania
Jeśli dane są kopiowane do usługi Azure Table Storage, ustaw **typ ujścia** z działaniem kopiowania, aby **AzureTableSink**i określ następujące właściwości w **ujścia** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, które mogą być używane przez obiekt sink. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, których wartości są używane jako klucze partycji. Jeśli nie zostanie określony, AzureTableDefaultPartitionKeyValue jest używany jako klucz partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, których wartości kolumny są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb Aby wstawić dane do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy istniejące wiersze w tabeli danych wyjściowych ze zgodnymi kluczami partycji i wiersza mają wartości zastąpienia lub scalony. <br/><br/>Aby dowiedzieć się, jak działają te ustawienia (scalania i Zastąp), zobacz [Insert lub Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) i [Insert lub Zastąp jednostki](https://msdn.microsoft.com/library/azure/hh452242.aspx) tematów. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli, a żadna opcja usuwa wiersze w tabeli danych wyjściowych, które nie istnieją w danych wejściowych. |Scal (ustawienie domyślne)<br/>Zastąp |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure, gdy writeBatchSize lub writeBatchTimeout tych limitów zostanie osiągnięty. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure, po osiągnięciu writeBatchSize lub writeBatchTimeout |TimeSpan<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (domyślnie magazynu klienta domyślna wartość limitu czasu wartości 90 s) |

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
Aby uzyskać więcej informacji na temat tych połączonych usług, zobacz [łącznika usługi Azure Table Storage](data-factory-azure-table-connector.md#copy-activity-properties) artykułu.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Amazon Redshift połączoną usługę, należy ustawić **typu** połączonej usługi, aby **AmazonRedshift**i określ następujące właściwości w **typeProperties** sekcji :

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| server |Adres IP lub hosta nazwę serwera usługi Amazon Redshift. |Yes |
| port |Numer portu TCP używanego przez serwer usługi Amazon Redshift do nasłuchiwania połączeń klientów. |Nie, wartość domyślna: 5439 |
| database |Nazwa bazy danych usługi Amazon Redshift. |Yes |
| username |Nazwa użytkownika, który ma dostęp do bazy danych. |Yes |
| password |Hasło dla konta użytkownika. |Tak |

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

Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika usługi Amazon Redshift.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi Amazon Redshift, ustaw **typu** zestawu danych na **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych usługi Amazon Redshift, których połączona usługa przywołuje. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |


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
Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika usługi Amazon Redshift.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z usługi Amazon Redshift, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

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
Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika usługi Amazon Redshift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować IBM DB2 połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesDB2**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| server |Nazwa serwera bazy danych DB2. |Tak |
| database |Nazwa bazy danych DB2. |Tak |
| Schemat |Nazwa schematu w bazie danych. Nazwa schematu jest uwzględniana wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych DB2. Możliwe wartości to: Anonimowe, podstawowe i Windows. |Tak |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowe lub Windows. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych DB2 w środowisku lokalnym. |Tak |

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
Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika programu IBM DB2.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych DB2, ustaw **typu** zestawu danych na **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych DB2, który połączona usługa przywołuje. Właściwość tableName jest uwzględniana wielkość liter. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona)

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

Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika programu IBM DB2.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z programu IBM DB2, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `"query": "select * from "MySchema"."MyTable""`. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

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
Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika programu IBM DB2.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować MySQL połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesMySql**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| server |Nazwa serwera MySQL. |Tak |
| database |Nazwa bazy danych MySQL. |Tak |
| Schemat |Nazwa schematu w bazie danych. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych MySQL. Możliwe wartości to: `Basic`. |Tak |
| username |Określ nazwę użytkownika do łączenia z bazą danych MySQL. |Tak |
| password |Określ hasło dla konta użytkownika, który określiłeś. |Tak |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych MySQL w środowisku lokalnym. |Tak |

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
Aby zdefiniować zestaw danych MySQL, ustaw **typu** zestawu danych na **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych MySQL, który połączona usługa przywołuje. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |

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

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych MySQL, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |


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
Aby zdefiniować Oracle połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesOracle**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| driverType | Określ, który sterownik do użycia w celu skopiowania danych z/do bazy danych Oracle. Dozwolone wartości to **Microsoft** lub **ODP** (ustawienie domyślne). W sekcji obsługiwane wersji i instalacji na szczegóły sterownika. | Nie |
| connectionString | Podaj informacje wymagane do nawiązania połączenia wystąpienia bazy danych programu Oracle dla właściwości connectionString. | Tak |
| gatewayName | Nazwa bramy, który jest używany do łączenia się z serwerem Oracle w środowisku lokalnym |Tak |

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

Aby uzyskać więcej informacji, zobacz [łącznik Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych Oracle, ustaw **typu** zestawu danych na **OracleTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Oracle, których połączona usługa przywołuje. |Nie (Jeśli **oracleReaderQuery** z **OracleSource** zostanie określona) |

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
Aby uzyskać więcej informacji, zobacz [łącznik Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) artykułu.

### <a name="oracle-source-in-copy-activity"></a>Źródło oprogramowania Oracle w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych Oracle, ustaw **typ źródła** z działaniem kopiowania, aby **OracleSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| oracleReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable` <br/><br/>Jeśli nie zostanie określony, instrukcję SQL, która jest wykonywana: `select * from MyTable` |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

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

Aby uzyskać więcej informacji, zobacz [łącznik Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artykułu.

### <a name="oracle-sink-in-copy-activity"></a>Oracle ujścia w działaniu kopiowania
Jeśli dane są kopiowane do am bazy danych Oracle database, ustaw **typ ujścia** z działaniem kopiowania, aby **elementu OracleSink**i określ następujące właściwości w **ujścia** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |TimeSpan<br/><br/> Przykład: 00:30:00 (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 100) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania w celu wypełnienia identyfikatorem automatycznie generowane wycinek, który jest używany do czyszczenia danych określonego wycinka czas ponownego. |Nazwa kolumny kolumny z typem danych binary(32). |Nie |

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
Aby uzyskać więcej informacji, zobacz [łącznik Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artykułu.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować PostgreSQL połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesPostgreSql**i określ następujące właściwości w **typeProperties** sekcja:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| server |Nazwa serwera PostgreSQL. |Tak |
| database |Nazwa bazy danych PostgreSQL. |Tak |
| Schemat |Nazwa schematu w bazie danych. Nazwa schematu jest uwzględniana wielkość liter. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych PostgreSQL. Możliwe wartości to: Anonimowe, podstawowe i Windows. |Tak |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowe lub Windows. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych postgresql w warstwie lokalnej. |Tak |

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
Aby uzyskać więcej informacji, zobacz [łącznika PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych postgresql w warstwie, ustaw **typu** zestawu danych na **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych PostgreSQL, który połączona usługa przywołuje. Właściwość tableName jest uwzględniana wielkość liter. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |

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
Aby uzyskać więcej informacji, zobacz [łącznika PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych PostgreSQL, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcja:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: "query": "Wybierz * z \"MySchema\".\" MyTable\"". |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

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

Aby uzyskać więcej informacji, zobacz [łącznika PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artykułu.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować SAP Business Warehouse (BW) połączoną usługę, należy ustawić **typu** połączonej usługi, aby **SapBw**i określ następujące właściwości w **typeProperties** sekcji :

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
server | Nazwa serwera, na którym znajduje się wystąpienie systemu SAP BW. | string | Tak
systemNumber | Numer systemu systemu SAP BW. | Liczba dziesiętna dwucyfrowy reprezentowane jako ciąg. | Tak
clientId | Identyfikator klienta klienta w SAP W systemie. | Liczba dziesiętna trzycyfrowy reprezentowane jako ciąg. | Tak
username | Nazwa użytkownika, który ma dostęp do serwera SAP | string | Tak
password | Hasło użytkownika. | string | Tak
gatewayName | Nazwa bramy, która powinna być używana w usłudze Data Factory, połączyć się z lokalnym wystąpieniem programu SAP BW. | string | Tak
encryptedCredential | Ciąg zaszyfrowane poświadczenia. | string | Nie

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
Aby zdefiniować zestaw danych SAP BW, ustaw **typu** zestawu danych na **RelationalTable**. Nie ma żadnych właściwości typu obsługiwanych dla zestawu danych SAP BW typu **RelationalTable**.

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

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli dane są kopiowane z SAP Business Warehouse, należy ustawić **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcja:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie MDX, które można odczytać danych z wystąpienia systemu SAP BW. | Zapytanie MDX. | Tak |

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
Aby zdefiniować platformy SAP HANA połączoną usługę, należy ustawić **typu** połączonej usługi, aby **SapHana**i określ następujące właściwości w **typeProperties** sekcji:

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
server | Nazwa serwera, na którym znajduje się wystąpienie SAP HANA. Jeśli serwer używa portu niestandardowe, należy określić `server:port`. | string | Tak
authenticationType | Typ uwierzytelnienia. | ciąg. "Podstawowa" lub "Windows" | Tak
username | Nazwa użytkownika, który ma dostęp do serwera SAP | string | Tak
password | Hasło użytkownika. | string | Tak
gatewayName | Nazwa bramy, która powinna być używana w usłudze Data Factory, połączyć się z lokalnym wystąpieniem oprogramowania SAP HANA. | string | Tak
encryptedCredential | Ciąg zaszyfrowane poświadczenia. | string | Nie

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
Aby zdefiniować zestaw danych SAP HANA, ustaw **typu** zestawu danych na **RelationalTable**. Nie ma żadnych właściwości typu obsługiwanych dla zestawu danych SAP HANA typu **RelationalTable**.

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

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z magazynu danych SAP HANA, należy ustawić **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie SQL do odczytywania danych z wystąpienia oprogramowania SAP HANA. | Zapytanie SQL. | Tak |


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
Tworzenie połączonej usługi typu **OnPremisesSqlServer** połączyć z lokalnej bazy danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla lokalnej połączonej usługi SQL Server.

Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla połączonej usługi SQL Server.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type powinna być równa: **OnPremisesSqlServer**. |Tak |
| connectionString |Określ informacje o parametry połączenia potrzebne do połączenia z bazą danych programu SQL Server w środowisku lokalnym przy użyciu uwierzytelniania SQL lub uwierzytelniania Windows. |Tak |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych programu SQL Server w środowisku lokalnym. |Tak |
| username |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania Windows. Przykład: **nazwa_domeny\\username**. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |

Umożliwia ona szyfrowanie poświadczeń za pomocą **New AzDataFactoryEncryptValue** polecenia cmdlet i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (**EncryptedCredential** właściwość):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: Kod JSON przy użyciu uwierzytelniania SQL

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
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: Plik JSON do korzystania z uwierzytelniania Windows

Jeśli nie określono nazwy użytkownika i hasła, brama używa ich do personifikacji konta określonego użytkownika, aby nawiązać połączenie z lokalną bazą danych programu SQL Server. W przeciwnym razie brama nawiązuje połączenie z serwerem SQL bezpośrednio za pomocą kontekstu zabezpieczeń bramy (jego konta uruchamiania).

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

Aby uzyskać więcej informacji, zobacz [łącznik programu SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych programu SQL Server, ustaw **typu** zestawu danych na **SqlServerTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych SQL Server, który połączona usługa przywołuje. |Tak |

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

Aby uzyskać więcej informacji, zobacz [łącznik programu SQL Server](data-factory-sqlserver-connector.md#dataset-properties) artykułu.

### <a name="sql-source-in-copy-activity"></a>Źródło SQL w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych programu SQL Server, należy ustawić **typ źródła** z działaniem kopiowania, aby **SqlSource**, a następnie określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. Może odwoływać się do wielu tabel z bazy danych odwołuje się zestaw danych wejściowych. Jeśli nie zostanie określony, instrukcję SQL, która jest wykonywana: Wybierz jedną z MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** określono SqlSource uruchomieniu działania kopiowania to zapytanie względem źródła danych programu SQL Server, można pobrać danych.

Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia wybrane zapytanie, aby uruchomić względem bazy danych programu SQL Server. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> Kiedy używasz **sqlReaderStoredProcedureName**, nadal należy określić wartość dla **tableName** właściwość w zestawie danych JSON. Nie ma żadnych operacji sprawdzania poprawności, mimo że wykonano zadania względem tej tabeli.


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

W tym przykładzie **sqlReaderQuery** dla SqlSource został określony. Działanie kopiowania uruchamia to zapytanie względem źródła danych programu SQL Server, można pobrać danych. Alternatywnie, można określić procedury składowanej, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli jest to procedura składowana pobiera parametry). SqlReaderQuery można odwoływać się do wielu tabel w bazie danych odwołuje się zestaw danych wejściowych. Nie jest ograniczona do tabeli, Ustaw jako typeProperty tableName typizowanego zestawu danych.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName kolumny zdefiniowane w sekcji struktury są używane do tworzenia wybrane zapytanie, aby uruchomić względem bazy danych programu SQL Server. Definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Aby uzyskać więcej informacji, zobacz [łącznik programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artykułu.

### <a name="sql-sink-in-copy-activity"></a>SQL ujścia w działaniu kopiowania
Jeśli dane są kopiowane do bazy danych programu SQL Server, ustaw **typ ujścia** z działaniem kopiowania, aby **SqlSink**i określ następujące właściwości w **ujścia** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania. |TimeSpan<br/><br/> Przykład: "00: 30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu. |Liczba całkowita (liczba wierszy) |Nie (domyślne: 10000) |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania do wykonania w taki sposób, że po oczyszczeniu danych określonego wycinka. Aby uzyskać więcej informacji zobacz sekcję powtarzalności. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania w celu wypełnienia identyfikatorem automatycznie generowane wycinek, który jest używany do czyszczenia danych określonego wycinka czas ponownego. Aby uzyskać więcej informacji zobacz sekcję powtarzalności. |Nazwa kolumny kolumny z typem danych binary(32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej danych wykonuje operację UPSERT (aktualizacje/wstawia) do tabeli docelowej. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Par nazwa/wartość. Nazwy i wielkość liter w wyrazie parametry muszą być zgodne, nazwy i wielkość liter w wyrazie parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Należy określić nazwę typu tabeli ma być używany w procedurze składowanej. Działanie kopiowania udostępnia dane jest przenoszony w tabeli tymczasowej w przypadku tego typu tabeli. Kod procedury składowanej można następnie scalić dane, w której są kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="example"></a>Przykład
Potoku zawierającego działanie kopiowania, który jest skonfigurowany do używania tych danych wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok **źródła** ustawiono typ **BlobSource** i **ujścia** ustawiono typ **SqlSink**.

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

Aby uzyskać więcej informacji, zobacz [łącznik programu SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artykułu.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Sybase połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesSybase**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| server |Nazwa serwera bazy danych Sybase. |Yes |
| database |Nazwa bazy danych programu Sybase. |Tak |
| Schemat |Nazwa schematu w bazie danych. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych programu Sybase. Możliwe wartości to: Anonimowe, podstawowe i Windows. |Tak |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowe lub Windows. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana w usłudze Data Factory, połączyć się z lokalną bazą danych programu Sybase. |Tak |

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
Aby zdefiniować zestaw danych programu Sybase, ustaw **typu** zestawu danych na **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych programu Sybase, który połączona usługa przywołuje. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |

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

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych programu Sybase, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji :


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

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
Aby zdefiniować Teradata połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesTeradata**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| server |Nazwa serwera programu Teradata. |Yes |
| authenticationType |Typ uwierzytelniania używany do łączenia z bazą danych programu Teradata. Możliwe wartości to: Anonimowe, podstawowe i Windows. |Tak |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowe lub Windows. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych Teradata w środowisku lokalnym. |Tak |

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

Aby uzyskać więcej informacji, zobacz [łącznika Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych obiektów Teradata Blob, ustaw **typu** zestawu danych na **RelationalTable**. Obecnie nie istnieją żadne właściwości typu obsługiwanych dla zestawu danych programu Teradata.

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

Aby uzyskać więcej informacji, zobacz [łącznika Teradata](data-factory-onprem-teradata-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych programu Teradata, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcja:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Tak |

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

Aby uzyskać więcej informacji, zobacz [łącznika Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties) artykułu.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować usługi połączonej bazy danych Cassandra, ustaw **typu** połączonej usługi, aby **OnPremisesCassandra**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| host |Jeden lub więcej adresów IP lub nazw hostów serwerów bazy danych Cassandra.<br/><br/>Określ rozdzielaną przecinkami listę adresów IP lub nazw hostów, połączyć się z wszystkich serwerów jednocześnie. |Yes |
| port |Port TCP, którego serwer Cassandra korzysta do nasłuchiwania połączeń klientów. |Nie, wartość domyślna: 9042 |
| authenticationType |Podstawowe lub anonimowe |Tak |
| username |Określ nazwę użytkownika dla konta użytkownika. |Tak, jeśli element authenticationType ustawiany jest podstawowy. |
| password |Określ hasło dla konta użytkownika. |Tak, jeśli element authenticationType ustawiany jest podstawowy. |
| gatewayName |Nazwa bramy, która służy do łączenia z bazą danych Cassandra w środowisku lokalnym. |Tak |
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

Aby uzyskać więcej informacji, zobacz [łącznika bazy danych Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych bazy danych Cassandra, ustaw **typu** zestawu danych na **CassandraTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| keyspace |Nazwa przestrzeni kluczy lub schemat bazy danych Cassandra. |Tak (Jeśli **zapytania** dla **CassandraSource** nie jest zdefiniowana). |
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

Aby uzyskać więcej informacji, zobacz [łącznika bazy danych Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties) artykułu.

### <a name="cassandra-source-in-copy-activity"></a>Źródło bazy danych Cassandra w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych Cassandra, ustaw **typ źródła** z działaniem kopiowania, aby **CassandraSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Zapytanie SQL 92 lub zapytanie języka CQL. Zobacz [odwołanie do języka CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Korzystając z zapytania SQL, określ **nazwa name.table przestrzeń kluczy** do reprezentowania tabeli, które chcesz zbadać. |Nie (jeśli są zdefiniowane tableName oraz przestrzeń kluczy w zestawie danych). |
| consistencyLevel |Poziom spójności Określa, jak wiele replik musi odpowiadać na żądania odczytu przed zwróceniem danych do aplikacji klienckiej. Bazy danych Cassandra sprawdza określoną liczbę replik dla danych do spełnienia żądania odczytu. |JEDEN, DWA, TRZY, KWORUM, WSZYSTKIE, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Zobacz [Konfigurowanie spójności danych](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) Aby uzyskać szczegółowe informacje. |Nie. Wartość domyślna to jeden. |

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

Aby uzyskać więcej informacji, zobacz [łącznika bazy danych Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties) artykułu.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Połączona usługa
Aby określić bazę danych MongoDB połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesMongoDB**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| server |Adres IP lub hosta nazwę serwera bazy danych MongoDB. |Yes |
| port |Port TCP używany przez serwer bazy danych MongoDB do nasłuchiwania połączeń klientów. |Opcjonalne, wartość domyślna: 27017 |
| authenticationType |Podstawowe, lub anonimowe. |Tak |
| username |Konto użytkownika, aby dostęp do bazy danych MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| password |Hasło użytkownika. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, który chcesz użyć, aby sprawdzić swoje poświadczenia dla uwierzytelniania. |Opcjonalnie (Jeśli używane jest uwierzytelnianie podstawowe). domyślne: korzysta z konta administratora i baza danych określona za pomocą właściwości databaseName. |
| databaseName |Nazwa bazy danych MongoDB, który chcesz uzyskać dostęp. |Tak |
| gatewayName |Nazwa bramy, który uzyskuje dostęp do magazynu danych. |Tak |
| encryptedCredential |Poświadczenie szyfrowane przez bramę. |Opcjonalne |

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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych bazy danych MongoDB, ustaw **typu** zestawu danych na **MongoDbCollection**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| collectionName |Nazwa kolekcji w bazie danych MongoDB. |Tak |

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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Źródłem bazy danych MongoDB w działaniu kopiowania
Jeśli kopiujesz dane z bazy danych MongoDB, ustaw **typ źródła** z działaniem kopiowania, aby **MongoDbSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL 92. Na przykład: `select * from MyTable`. |Nie (Jeśli **collectionName** z **dataset** zostanie określona) |

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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować Amazon S3 połączoną usługę, należy ustawić **typu** połączonej usługi, aby **AwsAccessKey**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| accessKeyID |Identyfikator klucza dostępu do kluczy tajnych. |string |Tak |
| secretAccessKey |Sam klucz dostępu do kluczy tajnych. |Zaszyfrowanego ciągu wpisu tajnego |Tak |

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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi Amazon S3, ustaw **typu** zestawu danych na **AmazonS3**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| bucketName |Nazwa zasobnika S3. |String |Tak |
| key |Klucz obiektu usługi S3. |String |Nie |
| prefix |Prefiks klucza obiektu S3. Zostaną zaznaczone obiekty, których klucze rozpoczynały od tego prefiksu. Ma zastosowanie tylko wtedy, gdy klucz jest pusty. |String |Nie |
| version |Wersja obiektu S3, jeśli jest włączone w wersji S3. |String |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie | |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie | |


> [!NOTE]
> Określa lokalizację obiektu S3, gdzie zasobnika jest nadrzędny kontener obiektów S3, a klucz jest pełną ścieżką do obiektu S3, bucketName + klawisz.

#### <a name="example-sample-dataset-with-prefix"></a>Przykład: Przykładowy zestaw danych z prefiksem

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
#### <a name="example-sample-data-set-with-version"></a>Przykład: Przykładowy zestaw danych (za pomocą wersji)

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
W tym przykładzie używamy stałe wartości dla właściwości key i bucketName w zestawie danych Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Program może obliczyć klucz i bucketName dynamicznie w czasie wykonywania przy użyciu zmiennych systemowych, takich jak SliceStart usługi fabryka danych.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Możesz tworzyć takie same dla właściwości prefiksu zestaw danych usługi Amazon S3. Zobacz [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md) Aby uzyskać listę obsługiwanych funkcji i zmiennych.

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli kopiujesz dane z usługi Amazon S3, ustaw **typ źródła** z działaniem kopiowania, aby **FileSystemSource**, a następnie określ następujące właściwości w **źródła** sekcji:


| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Określa, czy rekursywnie lista S3 obiektów w katalogu. |PRAWDA/FAŁSZ |Nie |


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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>System plików


### <a name="linked-service"></a>Połączona usługa
Możesz połączyć i lokalnym systemem plików usługi Azure data factory przy użyciu **na lokalnym serwerze plików** połączoną usługę. Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla serwera plików dla lokalnej połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Upewnij się, że właściwość typu jest ustawiona na **OnPremisesFileServer**. |Tak |
| host |Określa ścieżkę katalogu głównego folderu, który chcesz skopiować. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz definicje usługi i zestaw danych próbka połączone przykłady. |Tak |
| userid |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (Jeśli wybierzesz encryptedCredential) |
| password |Określ hasło dla użytkownika (nazwa użytkownika). |Nie (Jeśli wybierzesz encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzDataFactoryEncryptValue. |Nie (Jeśli chcesz określić identyfikator użytkownika i hasło w postaci zwykłego tekstu) |
| gatewayName |Określa nazwę bramy, która Data Factory powinna używać do łączenia się z serwerem plików lokalnych. |Tak |

#### <a name="sample-folder-path-definitions"></a>Ścieżka folderu definicje

| Scenariusz | Hosting w definicji połączonej usługi | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na maszynie bramy zarządzania danymi: <br/><br/>Przykłady: D:\\ \* lub D:\folder\subfolder\\* |D:\\ \\ (dla danych bramy zarządzania w wersji 2.0 i nowsze wersje) <br/><br/> localhost (dla starszych niż danych bramy zarządzania w wersji 2.0) |. \\ \\ lub folderu\\\\podfolder (dla danych bramy zarządzania w wersji 2.0 i nowsze wersje) <br/><br/>D:\\ \\ lub D:\\\\folderu\\\\podfolder (dla bramy w wersji 2.0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\myserver\\udostępnianie\\ \* lub \\ \\myserver\\udostępnianie\\folderu\\podfolderów\\* |\\\\\\\\MYSERVER\\\\udostępniania |. \\ \\ lub folderu\\\\podfolderów |


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

#### <a name="example-using-encryptedcredential"></a>Przykład: Za pomocą encryptedcredential

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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika systemu plików](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych systemu plików, ustaw **typu** zestawu danych na **udział plików**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Określa podrzędna ścieżka do folderu. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz definicje usługi i zestaw danych próbka połączone przykłady.<br/><br/>Można połączyć tę właściwość z **partitionBy** mieć folderu ścieżki, w oparciu o wycinek rozpoczęcia/zakończenia daty i godziny. |Tak |
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku jest w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr służący do wybierają podzbiór plików w ścieżce folderu, a nie wszystkich plików. <br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: "obiektu fileFilter": "* .log"<br/>Przykład 2: "obiektu fileFilter": 2016 - 1-?. txt"<br/><br/>Należy zauważyć, że tego obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych w udziale plików. |Nie |
| partitionedBy |PartitionedBy służy do określania dynamiczne folderPath/nazwę pliku do danych szeregów czasowych. Przykładem jest folderPath sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**; i są obsługiwane poziomy: **Optymalne** i **najszybszy**. zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika systemu plików](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli dane są kopiowane z systemu plików, ustaw **typ źródła** z działaniem kopiowania, aby **FileSystemSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. |Wartość true, False (domyślnie) |Nie |

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
Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>System plików ujścia w działaniu kopiowania
Jeśli dane są kopiowane do systemu plików, ustaw **typ ujścia** z działaniem kopiowania, aby **FileSystemSink**i określ następujące właściwości w **ujścia** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Definiuje zachowania dotyczącego kopiowania, gdy źródłem jest BlobSource lub systemu plików. |**PreserveHierarchy:** Zachowuje hierarchii plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna pliku docelowego, do folderu docelowego.<br/><br/>**FlattenHierarchy:** Wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone z nazwą wygenerowany automatycznie.<br/><br/>**MergeFiles:** Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli określono nazwę pliku nazwy/obiekt blob, nazwa pliku scalonego jest określona nazwa. W przeciwnym razie jest to nazwa pliku generowanych automatycznie. |Nie |

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

Aby uzyskać więcej informacji, zobacz [artykuł dotyczący łącznika systemu plików](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>Protokół FTP

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować FTP połączoną usługę, należy ustawić **typu** połączonej usługi, aby **SerwerFTP**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane | Domyślne |
| --- | --- | --- | --- |
| host |Nazwa lub adres IP serwera FTP |Tak |&nbsp; |
| authenticationType |Określanie typu uwierzytelniania |Tak |Podstawowe i anonimowe |
| username |Użytkownik mający dostęp do serwera FTP |Nie |&nbsp; |
| password |Hasło dla użytkownika (nazwa użytkownika) |Nie |&nbsp; |
| encryptedCredential |Zaszyfrowane poświadczenia dostępu do serwera FTP |Nie |&nbsp; |
| gatewayName |Nazwa bramy zarządzania danymi, aby nawiązać połączenie z serwerem FTP w środowisku lokalnym |Nie |&nbsp; |
| port |Port, na którym nasłuchuje serwer FTP |Nie |21 |
| enableSsl |Określ, czy należy użyć serwera FTP za pośrednictwem kanału SSL/TLS |Nie |true |
| enableServerCertificateValidation |Określ, czy włączyć weryfikacji certyfikatu SSL serwera przy użyciu protokołu FTP za pośrednictwem kanału SSL/TLS |Nie |true |

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

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Przykład: Za pomocą portu, enableSsl, enableServerCertificateValidation

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Przykład: Używanie encryptedCredential na potrzeby uwierzytelniania i bramy

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
Aby zdefiniować zestaw danych usługi FTP, ustaw **typu** zestawu danych na **udział plików**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna w folderze. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz definicje usługi i zestaw danych próbka połączone przykłady.<br/><br/>Można połączyć tę właściwość z **partitionBy** mieć folderu ścieżki, w oparciu o wycinek rozpoczęcia/zakończenia daty i godziny. |Tak
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku byłoby w następującym tego formatu: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr służący do wybierają podzbiór plików w ścieżce folderu, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykłady 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych w udziale plików. Ta właściwość nie jest obsługiwana przy użyciu systemu plików HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwa_pliku danych szeregów czasowych. Na przykład folderPath sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**; i są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ czy używać trybu binarnego transferu. Wartość true na false kodów ASCII i binarny trybu. Wartość domyślna: True. Ta właściwość jest używana tylko w przypadku, gdy skojarzonej połączonej usługi, typ jest typem: FtpServer. |Nie |

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

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli dane są kopiowane z serwera FTP, ustaw **typ źródła** z działaniem kopiowania, aby **FileSystemSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. |Wartość true, False (domyślnie) |Nie |

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


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować systemu plików HDFS połączoną usługę, należy ustawić **typu** połączonej usługi, aby **systemu plików Hdfs**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type musi być równa: **Hdfs** |Tak |
| Adres URL |Adres URL do systemu plików HDFS |Tak |
| authenticationType |Anonimowe lub Windows. <br><br> Aby użyć **uwierzytelnianie Kerberos** łącznika systemu plików HDFS można znaleźć w tej sekcji, aby odpowiednio skonfigurować środowisko w środowisku lokalnym. |Tak |
| userName |Uwierzytelnianie nazwy użytkownika dla Windows. |Tak (dla uwierzytelniania Windows) |
| password |Hasło do uwierzytelniania Windows. |Tak (dla uwierzytelniania Windows) |
| gatewayName |Nazwa bramy, która powinna być używana w usłudze Data Factory, połączyć się z systemu plików HDFS. |Tak |
| encryptedCredential |[Nowe AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) poświadczeń dostępu do danych wyjściowych. |Nie |

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

#### <a name="example-using-windows-authentication"></a>Przykład: Przy użyciu uwierzytelniania Windows

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

Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika systemu plików HDFS.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych systemu plików HDFS, ustaw **typu** zestawu danych na **udział plików**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka do folderu. Przykład: `myfolder`<br/><br/>Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Na przykład: w przypadku folder\subfolder, należy określić folder\\\\podfolder i d:\samplefolder, określ d:\\\\folder_przykładowy.<br/><br/>Można połączyć tę właściwość z **partitionBy** mieć folderu ścieżki, w oparciu o wycinek rozpoczęcia/zakończenia daty i godziny. |Tak |
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku byłoby w następującym tego formatu: <br/><br/>`Data.<Guid>.txt` (na przykład:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwa_pliku danych szeregów czasowych. Przykład: folderPath, sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

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

Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika systemu plików HDFS.

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli dane są kopiowane z systemu plików HDFS, ustaw **typ źródła** z działaniem kopiowania, aby **FileSystemSource**, a następnie określ następujące właściwości w **źródła** sekcji:

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. |Wartość true, False (domyślnie) |Nie |

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

Aby uzyskać więcej informacji zobacz artykuł dotyczący łącznika systemu plików HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować SFTP połączoną usługę, należy ustawić **typu** połączonej usługi, aby **Sftp**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| host | Nazwa lub adres IP serwera SFTP. |Tak |
| port |Port, na którym nasłuchuje serwer SFTP. Wartość domyślna to: 21 |Nie |
| authenticationType |Określ typ uwierzytelniania. Dozwolone wartości: **Podstawowe**, **SshPublicKey**. <br><br> Odnoszą się do uwierzytelniania podstawowego Using i [przy użyciu protokołu SSH uwierzytelnianie klucza publicznego](#using-ssh-public-key-authentication) odpowiednio sekcje więcej właściwości i przykłady kodu JSON. |Tak |
| skipHostKeyValidation | Określ, czy pominąć sprawdzanie poprawności klucza hosta. | Nie. Wartość domyślna: false |
| hostKeyFingerprint | Określ odcisk palca klucza hosta. | Tak, jeśli `skipHostKeyValidation` jest ustawiona na wartość false.  |
| gatewayName |Nazwa bramy zarządzania danymi, aby nawiązać połączenie z lokalnym serwerem SFTP. | Tak, jeśli kopiowanie danych z lokalnego serwera SFTP. |
| encryptedCredential | Zaszyfrowane poświadczenia dostępu do serwera SFTP. Wygenerowany automatycznie po określeniu w kreatorze kopiowania lub okno podręczne ClickOnce uwierzytelniania podstawowego (nazwa użytkownika i hasło) lub uwierzytelniania klucz publiczny SSH (nazwa użytkownika i ścieżki klucza prywatnego lub zawartości). | Nie. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |

#### <a name="example-using-basic-authentication"></a>Przykład: Przy użyciu uwierzytelniania podstawowego

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `Basic`, a następnie określ następujące właściwości oprócz tych ogólnych wprowadzone w ostatniej sekcji łącznika SFTP:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| username | Użytkownik, który ma dostęp do serwera SFTP. |Tak |
| password | Hasło dla użytkownika (nazwa użytkownika). | Tak |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Przykład: **Podstawowe uwierzytelnianie za pomocą zaszyfrowanych poświadczeń**

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

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `SshPublicKey`, a następnie określ następujące właściwości oprócz tych ogólnych wprowadzone w ostatniej sekcji łącznika SFTP:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| username |Użytkownik, który ma dostęp do serwera SFTP |Tak |
| privateKeyPath | Określ ścieżkę bezwzględną do pliku klucza prywatnego może dostęp do tej bramy. | Wybierz opcję `privateKeyPath` lub `privateKeyContent`. <br><br> Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera SFTP. |
| privateKeyContent | Ciąg serializacji treści klucza prywatnego. Kreator kopiowania można odczytać pliku klucza prywatnego i automatycznie wyodrębnić zawartość klucza prywatnego. Jeśli używane są wszystkie inne narzędzia/zestawu SDK, należy użyć właściwości privateKeyPath. | Wybierz opcję `privateKeyPath` lub `privateKeyContent`. |
| passPhrase | Określ — dostęp próbny frazy/hasło do odszyfrowania klucza prywatnego, jeśli plik klucza, który jest chroniony przez frazę. | Tak, czy plik klucza prywatnego jest chroniony przez frazę. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Przykład: **Klucz publiczny SSH uwierzytelniania za pomocą prywatnego klucza zawartości**

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
Aby zdefiniować zestaw SFTP, ustaw **typu** zestawu danych na **udział plików**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Ścieżka podrzędna w folderze. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz definicje usługi i zestaw danych próbka połączone przykłady.<br/><br/>Można połączyć tę właściwość z **partitionBy** mieć folderu ścieżki, w oparciu o wycinek rozpoczęcia/zakończenia daty i godziny. |Tak |
| fileName |Określ nazwę pliku w **folderPath** chcącym tabeli do odwoływania się do określonego pliku w folderze. Jeśli nie określisz żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Jeśli nie określono nazwy pliku dla wyjściowego zestawu danych, nazwę wygenerowanego pliku byłoby w następującym tego formatu: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| fileFilter |Określ filtr służący do wybierają podzbiór plików w ścieżce folderu, a nie wszystkich plików.<br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykłady 1: `"fileFilter": "*.log"`<br/>Przykład 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych w udziale plików. Ta właściwość nie jest obsługiwana przy użyciu systemu plików HDFS. |Nie |
| partitionedBy |partitionedBy może służyć do określenia dynamiczne folderPath, nazwa_pliku danych szeregów czasowych. Na przykład folderPath sparametryzowane za każdą godzinę danych. |Nie |
| format | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |
| useBinaryTransfer |Określ czy używać trybu binarnego transferu. Wartość true na false kodów ASCII i binarny trybu. Wartość domyślna: True. Ta właściwość jest używana tylko w przypadku, gdy skojarzonej połączonej usługi, typ jest typem: FtpServer. |Nie |

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

### <a name="file-system-source-in-copy-activity"></a>Źródło systemu plików w działaniu kopiowania
Jeśli kopiujesz dane ze źródła SFTP, ustaw **typ źródła** z działaniem kopiowania, aby **FileSystemSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| recursive |Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. |Wartość true, False (domyślnie) |Nie |



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
Aby zdefiniować HTTP połączoną usługę, należy ustawić **typu** połączonej usługi, aby **Http**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| url | Podstawowy adres URL do serwera sieci Web | Tak |
| authenticationType | Określa typ uwierzytelniania. Dozwolone wartości to: **Anonimowe**, **podstawowe**, **szyfrowanego**, **Windows**, **ClientCertificate**. <br><br> Zapoznaj się sekcje poniżej tej tabeli na więcej właściwości i przykłady kodu JSON dla tych typów uwierzytelniania, odpowiednio. | Tak |
| enableServerCertificateValidation | Określ, czy włączyć weryfikacji certyfikatu SSL serwera, jeśli źródło jest serwer sieci Web protokołu HTTPS | Nie, wartość domyślna to true |
| gatewayName | Nazwa bramy zarządzania danymi, aby nawiązać połączenie z lokalnym źródłem HTTP. | Tak, jeśli kopiowanie danych z lokalnego źródła HTTP. |
| encryptedCredential | Zaszyfrowane poświadczenia, aby dostęp do punktu końcowego HTTP. Wygenerowany automatycznie podczas konfigurowania informacji o uwierzytelnianiu w kreatorze kopiowania lub okno podręczne ClickOnce. | Nie. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego serwera HTTP. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Przykład: Uwierzytelnianie podstawowe, szyfrowane lub Windows
Ustaw `authenticationType` jako `Basic`, `Digest`, lub `Windows`i określ następujące właściwości oprócz łącznik protokołu HTTP ogólnego z nich wprowadzono powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| username | Nazwa użytkownika do dostępu do punktu końcowego HTTP. | Tak |
| password | Hasło dla użytkownika (nazwa użytkownika). | Tak |

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

Aby użyć uwierzytelniania podstawowego, ustaw `authenticationType` jako `ClientCertificate`i określ następujące właściwości oprócz łącznik protokołu HTTP ogólnego z nich wprowadzono powyżej:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| embeddedCertData | Zawartość algorytmem Base64 dane binarne z pliku wymiany informacji osobistych (PFX). | Wybierz opcję `embeddedCertData` lub `certThumbprint`. |
| certThumbprint | Odcisk palca certyfikatu, który został zainstalowany na maszynie bramy magazynu certyfikatów. Mają zastosowanie tylko wtedy, gdy kopiowanie danych z lokalnego źródła HTTP. | Wybierz opcję `embeddedCertData` lub `certThumbprint`. |
| password | Hasło skojarzone z tym certyfikatem. | Nie |

Jeśli używasz `certThumbprint` dla uwierzytelniania i certyfikat został zainstalowany w magazynie osobistym komputera lokalnego, musisz udzielić uprawnień do odczytu do usługi bramy:

1. Uruchom program Microsoft Management Console (MMC). Dodaj **certyfikaty** przystawki przeznaczonego dla **komputera lokalnego**.
2. Rozwiń **certyfikaty**, **osobistych**i kliknij przycisk **certyfikaty**.
3. Kliknij prawym przyciskiem myszy certyfikat w magazynie osobistym, a następnie wybierz pozycję **wszystkie zadania**->**Zarządzaj kluczami prywatnymi...**
3. Na **zabezpieczeń** pozycję Dodaj konto użytkownika, w którym usługa Host bramy zarządzania danymi działa z dostępem do odczytu do certyfikatu.

**Przykład: przy użyciu certyfikatu klienta:** Ta połączona usługa łączy fabryki danych na lokalnym serwerze sieci web HTTP. Certyfikat klienta, który jest instalowany na komputerze z bramę zarządzania danymi zainstalowaną go używa.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Przykład: użycie certyfikatu klienta w pliku
Ta połączona usługa łączy fabryki danych na lokalnym serwerze sieci web HTTP. Za pomocą pliku certyfikatu klienta na komputerze zainstalowano bramę zarządzania danymi.

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

Aby uzyskać więcej informacji, zobacz [łącznik protokołu HTTP](data-factory-http-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych protokołu HTTP, ustaw **typu** zestawu danych na **Http**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| relativeUrl | Względny adres URL do zasobu, który zawiera dane. Jeśli ścieżka nie zostanie określona, używana jest tylko adres URL, które są określone w definicji połączonej usługi. <br><br> Aby skonstruować dynamicznego adresu URL, możesz użyć [funkcji usługi fabryka danych i zmiennych systemowych](data-factory-functions-variables.md), przykład: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nie |
| requestMethod | Metoda HTTP. Dozwolone wartości to **UZYSKAĆ** lub **WPIS**. | Nie. Wartość domyślna to `GET`. |
| additionalHeaders | Dodatkowe nagłówki żądania HTTP. | Nie |
| requestBody | Treść żądania HTTP. | Nie |
| format | Jeśli chcesz po prostu **pobierają dane z punktu końcowego HTTP jako — jest** bez podczas analizowania, Pomiń ten format ustawień. <br><br> Jeśli chcesz przeanalizować zawartości odpowiedzi HTTP podczas kopiowania, obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Aby uzyskać więcej informacji, zobacz [Format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. |Nie |
| compression | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy: **Optymalne** i **najszybszy**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

#### <a name="example-using-the-get-default-method"></a>Przykład: użycie metody GET (ustawienie domyślne)

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

#### <a name="example-using-the-post-method"></a>Przykład: użycie metody POST

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
Aby uzyskać więcej informacji, zobacz [łącznik protokołu HTTP](data-factory-http-connector.md#dataset-properties) artykułu.

### <a name="http-source-in-copy-activity"></a>Źródło HTTP w działaniu kopiowania
Jeśli kopiujesz dane ze źródła HTTP, ustaw **typ źródła** z działaniem kopiowania, aby **HttpSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| httpRequestTimeout | Limit czasu (przedział czasu) dla żądania HTTP można uzyskać odpowiedzi. Limit czasu jest sposobem uzyskania odpowiedzi, a nie limitu czasu można odczytać danych odpowiedzi. | Nie. Wartość domyślna: 00:01:40 |


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

Aby uzyskać więcej informacji, zobacz [łącznik protokołu HTTP](data-factory-http-connector.md#copy-activity-properties) artykułu.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować ze źródłem danych OData połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OData**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| url |Adres URL usługi OData. |Tak |
| authenticationType |Typ uwierzytelniania używany do łączenia z źródła OData. <br/><br/> W chmurze OData możliwe wartości to anonimowe, podstawowe i OAuth (Uwaga obsługują obecnie tylko usługę Azure Data Factory usługi Azure Active Directory na podstawie OAuth). <br/><br/> Dla protokołu OData w środowisku lokalnym możliwe wartości to anonimowe, podstawowe i Windows. |Tak |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Tak (tylko wtedy, gdy używasz uwierzytelniania podstawowego) |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Tak (tylko wtedy, gdy używasz uwierzytelniania podstawowego) |
| authorizedCredential |Jeśli używasz uwierzytelniania OAuth, kliknij przycisk **Autoryzuj** znajdujący się w kreatora kopiowania usługi Data Factory lub edytorze, a następnie wprowadź swoje poświadczenia, wartość tej właściwości zostanie wygenerowany automatycznie. |Tak (tylko w przypadku korzystania z uwierzytelniania OAuth) |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do połączenia z usługą OData w środowisku lokalnym. Określ tylko, jeśli kopiujesz dane z lokalnego źródła OData. |Nie |

#### <a name="example---using-basic-authentication"></a>Przykład — przy użyciu uwierzytelniania podstawowego
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
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
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Przykład — Windows przy użyciu uwierzytelniania uzyskiwania dostępu do lokalnego źródła OData

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Przykład — za pomocą uwierzytelniania OAuth, uzyskiwanie dostępu do chmury źródła OData
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

Aby uzyskać więcej informacji, zobacz [łącznik OData](data-factory-odata-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi OData, ustaw **typu** zestawu danych na **ODataResource**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| path |Ścieżka do zasobu OData |Nie |

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

Aby uzyskać więcej informacji, zobacz [łącznik OData](data-factory-odata-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane ze źródła danych OData, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Przykład | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |"?$select=Name, Description&$top=5" |Nie |

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

Aby uzyskać więcej informacji, zobacz [łącznik OData](data-factory-odata-connector.md#copy-activity-properties) artykułu.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować ODBC połączoną usługę, należy ustawić **typu** połączonej usługi, aby **OnPremisesOdbc**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| connectionString |Poświadczenia dostępu swobodnego część ciąg połączenia i opcjonalnie zaszyfrowane poświadczenia. Zobacz przykłady w poniższych sekcjach. |Tak |
| poświadczenia |Dostęp do poświadczeń część parametrów połączenia określonych w formacie wartości właściwości specyficzne dla sterownika. Przykład: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC. Możliwe wartości to: Anonimowy i podstawowa. |Tak |
| username |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która powinna być używana w usłudze Data Factory, aby nawiązać połączenie z magazynem danych ODBC. |Tak |

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Przykład — przy użyciu poświadczeń zaszyfrowane przy użyciu uwierzytelniania podstawowego
Umożliwia ona szyfrowanie poświadczeń za pomocą [New AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) polecenia cmdlet.

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

Aby uzyskać więcej informacji, zobacz [łącznik ODBC](data-factory-odbc-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych ODBC, ustaw **typu** zestawu danych na **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w magazynie danych ODBC. |Tak |


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

Aby uzyskać więcej informacji, zobacz [łącznik ODBC](data-factory-odbc-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z magazynu danych ODBC, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji :

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Ciąg zapytania SQL. Na przykład: `select * from MyTable`. |Tak |

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

Aby uzyskać więcej informacji, zobacz [łącznik ODBC](data-factory-odbc-connector.md#copy-activity-properties) artykułu.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Połączona usługa
Do usługi Salesforce do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **Salesforce**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| environmentUrl | Określ wystąpienie adres URL usługi Salesforce. <br><br> -Wartością domyślną jest "https:\//login.salesforce.com". <br> -Aby skopiować dane z piaskownicy, należy określić "https://test.salesforce.com". <br> — Aby skopiować dane z domeny niestandardowej, określić, na przykład "https://[domain].my.salesforce.com". |Nie |
| username |Określ nazwę użytkownika dla konta użytkownika. |Yes |
| password |Określ hasło dla konta użytkownika. |Tak |
| securityToken |Określ token zabezpieczeń dla konta użytkownika. Zobacz [uzyskać token zabezpieczający](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) instrukcje na temat sposobu resetowania/get tokenu zabezpieczającego. Aby dowiedzieć się więcej o tokenów zabezpieczających ogólnie rzecz biorąc, zobacz [zabezpieczeń i interfejsu API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Tak |

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

Aby uzyskać więcej informacji, zobacz [łącznik programu Salesforce](data-factory-salesforce-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych usługi Salesforce, ustaw **typu** zestawu danych na **RelationalTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w usłudze Salesforce. |Nie (Jeśli **zapytania** z **RelationalSource** zostanie określona) |

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

Aby uzyskać więcej informacji, zobacz [łącznik programu Salesforce](data-factory-salesforce-connector.md#dataset-properties) artykułu.

### <a name="relational-source-in-copy-activity"></a>Źródło relacyjne w działaniu kopiowania
Jeśli kopiujesz dane z usług Salesforce, ustaw **typ źródła** z działaniem kopiowania, aby **RelationalSource**, a następnie określ następujące właściwości w **źródła** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowe można odczytać danych. |Zapytanie SQL 92 lub [Salesforce obiektu Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) zapytania. Przykład: `select * from MyTable__c`. |Nie (Jeśli **tableName** z **dataset** zostanie określona) |

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
> Część "__c" Nazwa interfejsu API jest wymagany dla dowolnego obiektu niestandardowego.

Aby uzyskać więcej informacji, zobacz [łącznik programu Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) artykułu.

## <a name="web-data"></a>Dane sieci Web

### <a name="linked-service"></a>Połączona usługa
Aby zdefiniować sieci Web połączoną usługę, należy ustawić **typu** połączonej usługi, aby **sieci Web**i określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Adres URL |Adres URL źródła w sieci Web |Tak |
| authenticationType |Anonimowe. |Tak |


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

Aby uzyskać więcej informacji, zobacz [Tabela sieci Web łącznika](data-factory-web-table-connector.md#linked-service-properties) artykułu.

### <a name="dataset"></a>Zestaw danych
Aby zdefiniować zestaw danych w sieci Web, ustaw **typu** zestawu danych na **WebTable**, a następnie określ następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Typ zestawu danych. musi być równa **WebTable** |Tak |
| path |Względny adres URL do zasobu, który zawiera tabelę. |Nie. Jeśli ścieżka nie zostanie określona, używana jest tylko adres URL, które są określone w definicji połączonej usługi. |
| index |Indeks tabeli w zasobie. Zobacz Get indeksu tabeli w sekcji strony HTML instrukcje pobierania indeksu tabeli na stronie HTML. |Tak |

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

Aby uzyskać więcej informacji, zobacz [Tabela sieci Web łącznika](data-factory-web-table-connector.md#dataset-properties) artykułu.

### <a name="web-source-in-copy-activity"></a>Źródło w sieci Web w działaniu kopiowania
Jeśli kopiujesz dane z tabeli w sieci web, należy ustawić **typ źródła** z działaniem kopiowania, aby **WebSource**. Obecnie, gdy źródłowego w działaniu kopiowania jest typu **WebSource**, żadne dodatkowe właściwości są obsługiwane.

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

Aby uzyskać więcej informacji, zobacz [Tabela sieci Web łącznika](data-factory-web-table-connector.md#copy-activity-properties) artykułu.

## <a name="compute-environments"></a>ŚRODOWISKA OBLICZENIOWEGO
Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez usługi Data Factory i działania przekształcania, które można uruchomić na nich. Kliknij link do obliczeń, który chcesz wyświetlić schematów JSON dla połączonej usługi połączyć je z fabryką danych.

| Środowisko obliczeniowe | Działania |
| --- | --- |
| [Klaster HDInsight na żądanie](#on-demand-azure-hdinsight-cluster) lub [klaster HDInsight](#existing-azure-hdinsight-cluster) |[Niestandardowe działanie platformy .NET](#net-custom-activity), [działania programu Hive](#hdinsight-hive-activity), [Pig działania](#hdinsight-pig-activity), [działania technologii MapReduce](#hdinsight-mapreduce-activity), przesyłanie strumieniowe działania usługi Hadoop [działania Spark](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Niestandardowe działanie platformy .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Działanie wykonywania wsadowego Machine Learning](#machine-learning-batch-execution-activity), [usługi Machine Learning działanie aktualizacji zasobu](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Język U-SQL usługi Data Lake Analytics](#data-lake-analytics-u-sql-activity) |
| [Usługa Azure SQL Database](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [programu SQL Server](#sql-server-1) |[Procedura składowana](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Klaster usługi Azure HDInsight na żądanie
Usługa Azure Data Factory może automatycznie tworzyć Windows/Linux-based klastra HDInsight na żądanie do przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu (właściwość linkedServiceName w formacie JSON) skojarzonego z klastrem. Można wykonać następujące działania przekształcania na tej połączonej usługi: [niestandardowe działanie platformy .NET](#net-custom-activity), [działania programu Hive](#hdinsight-hive-activity), [Pig działania](#hdinsight-pig-activity), [MapReduce działanie](#hdinsight-mapreduce-activity), przesyłanie strumieniowe działania usługi Hadoop [prowadzenie działalności](#hdinsight-spark-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opis właściwości używane w definicji JSON usługi Azure, usługi połączonej HDInsight na żądanie.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type powinna być równa **HDInsightOnDemand**. |Tak |
| clusterSize |Liczba węzłów procesu roboczego/danych w klastrze. Klaster HDInsight jest tworzony z 2 węzłami głównymi wraz z liczbą węzłów procesów roboczych, które określisz dla tej właściwości. Węzły są o rozmiarze maszyna wirtualna Standard_D3, który ma 4 rdzenie, dzięki czemu klaster z węzłami procesu roboczego 4 przyjmuje 24 rdzenie (4\*4 = 16 rdzeni dla węzłów procesu roboczego oraz 2\*4 = 8 rdzeni dla węzłów głównych). Zobacz [opartych na systemie Linux z Tworzenie klastrów usługi Hadoop w HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) szczegółowe informacje na temat warstwy maszyna wirtualna Standard_D3. |Tak |
| timetolive |Dozwolony czas bezczynności, po dla klastra HDInsight na żądanie. Określa, jak długo klastra HDInsight na żądanie pozostanie aktywny po zakończeniu działania uruchamiania w przypadku żadnych aktywnych działań w klastrze.<br/><br/>Na przykład po uruchomienia działania trwa 6 minut i timetolive jest ustawiony na 5 minut, klaster pozostanie aktywny przez 5 minut po uruchomieniu 6 minut operacji przetwarzania działania. Jeśli uruchomienie innego działania jest wykonywane przy użyciu okna 6 minut, jest on przetwarzany przez tego samego klastra.<br/><br/>Tworzenie klastra usługi HDInsight na żądanie jest kosztowną operacją (może to trochę potrwać), użyj tak, to ustawienie jako potrzebnych do zwiększenia wydajności usługi data factory dzięki ponownemu wykorzystaniu klastra usługi HDInsight na żądanie.<br/><br/>Jeśli wartość timetolive jest ustawiona na wartość 0, klaster jest usuwany, jak działanie uruchamiane przetworzone. Z drugiej strony Jeśli ustawisz o wysokiej wartości, klaster może pozostać bezczynny, co niepotrzebnie wysokich kosztów. Dlatego ważne jest, ustaw odpowiednią wartość zgodnie z potrzebami.<br/><br/>Wiele potoków można udostępnić tego samego wystąpienia klastra HDInsight na żądanie, jeśli skonfigurowana wartość timetolive właściwości |Tak |
| version |Wersja klastra HDInsight. Aby uzyskać więcej informacji, zobacz [obsługiwanych wersjach HDInsight w usłudze Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nie |
| linkedServiceName |Połączona usługa Azure Storage używanego przez klaster na żądanie do przechowywania i przetwarzania danych. <p>Obecnie nie można utworzyć klastra HDInsight na żądanie, która używa usługi Azure Data Lake Store jako magazyn. Jeśli chcesz przechowywać dane wynikowe z HDInsight przetwarzania w usłudze Azure Data Lake Store umożliwia działanie kopiowania skopiuj dane z usługi Azure Blob Storage do usługi Azure Data Lake Store.</p>  | Tak |
| additionalLinkedServiceNames |Określa, że dodatkowe konta magazynu dla HDInsight połączonej usługi, tak aby usługa Data Factory można zarejestrować je w Twoim imieniu. |Nie |
| osType |Typ systemu operacyjnego. Dozwolone wartości to: Windows (ustawienie domyślne) i Linux |Nie |
| hcatalogLinkedServiceName |Nazwa programu SQL Server, Azure połączoną usługę, wskazujące HCatalog bazy danych. Klaster HDInsight na żądanie jest tworzony przy użyciu bazy danych Azure SQL jako magazynu metadanych. |Nie |

### <a name="json-example"></a>Przykład kodu JSON
Następujący kod JSON definiuje opartych na systemie Linux usługi połączonej HDInsight na żądanie. Usługa Data Factory automatycznie tworzy **opartych na systemie Linux** klastra HDInsight podczas przetwarzania wycinka danych.

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

Aby uzyskać więcej informacji, zobacz [usługi połączone usługi Compute](data-factory-compute-linked-services.md) artykułu.

## <a name="existing-azure-hdinsight-cluster"></a>Istniejący klaster usługi Azure HDInsight
Można utworzyć usługi Azure HDInsight połączone do zarejestrowania własnego klastra HDInsight z usługą Data Factory. Można wykonać następujące działania przekształcania danych na tej połączonej usługi: [niestandardowe działanie platformy .NET](#net-custom-activity), [działania programu Hive](#hdinsight-hive-activity), [Pig działania](#hdinsight-pig-activity), [ Działania technologii MapReduce](#hdinsight-mapreduce-activity), przesyłanie strumieniowe działania usługi Hadoop [prowadzenie działalności](#hdinsight-spark-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opis właściwości używane w definicji JSON usługi Azure HDInsight Azure połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type powinna być równa **HDInsight**. |Tak |
| clusterUri |Identyfikator URI klastra HDInsight. |Tak |
| username |Podaj nazwę użytkownika, który ma być używany do łączenia z do istniejącego klastra HDInsight. |Tak |
| password |Określ hasło dla konta użytkownika. |Tak |
| linkedServiceName | Nazwa połączonej usługi Azure Storage odwołująca się do usługi Azure blob storage używane przez klaster usługi HDInsight. <p>Obecnie nie można określić, że usługi Azure Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster HDInsight ma dostęp do Data Lake Store może dostęp do danych w usłudze Azure Data Lake Store z skryptów usługi Hive/Pig. </p>  |Tak |

Dla wersji klastrów HDInsight obsługiwane, zobacz [obsługiwane wersje serwera HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>Przykład kodu JSON

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
Usługa Azure Batch połączone do zarejestrowania puli usługi Batch maszyn wirtualnych (VM) można utworzyć za pomocą usługi data factory. Można uruchomić działania niestandardowe platformy .NET przy użyciu usługi Azure Batch lub usługi Azure HDInsight. Możesz uruchomić [niestandardowe działanie platformy .NET](#net-custom-activity) na tej połączonej usługi.

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opis właściwości używane w definicji JSON usługi Azure, usługi Azure Batch połączone.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type powinna być równa **AzureBatch**. |Tak |
| accountName |Nazwa konta usługi Azure Batch. |Tak |
| accessKey |Klucz dostępu dla konta usługi Azure Batch. |Tak |
| poolName |Nazwa puli maszyn wirtualnych. |Tak |
| linkedServiceName |Nazwa usługi Azure Storage połączone skojarzonego z tą usługą Azure Batch połączonej usługi. Ta połączona usługa jest używana dla tymczasowe pliki wymagane do uruchomienia działania i przechowywane dzienniki wykonywania działania. |Tak |


#### <a name="json-example"></a>Przykład kodu JSON

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
Utworzysz usługę Azure Machine Learning połączone do zarejestrowania punktu końcowego za pomocą usługi data factory oceniania partii usługi Machine Learning. Dwa działania dotyczące przekształcania danych, które można uruchomić w tej połączonej usługi: [Działanie wykonywania wsadowego Machine Learning](#machine-learning-batch-execution-activity), [usługi Machine Learning działanie aktualizacji zasobu](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Połączona usługa
Poniższa tabela zawiera opis właściwości używane w definicji JSON usługi Azure, Azure Machine Learning w przypadku połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Type |Właściwość type powinna być równa: **AzureML**. |Tak |
| mlEndpoint |Adres URL wsadowego oceniania. |Tak |
| ApiKey |Interfejs API opublikowanego modelu obszaru roboczego firmy. |Tak |

#### <a name="json-example"></a>Przykład kodu JSON

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
Możesz utworzyć **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługi Azure Data Lake Analytics obliczeń Usługa do usługi Azure data factory, przed rozpoczęciem korzystania z [działanie U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md) w potoku.

### <a name="linked-service"></a>Połączona usługa

Poniższa tabela zawiera opis właściwości używane w definicji JSON usługi połączonej usługi Azure Data Lake Analytics.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| Type |Właściwość type powinna być równa: **AzureDataLakeAnalytics**. |Tak |
| accountName |Nazwa konta usługi Azure Data Lake Analytics. |Tak |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI. |Nie |
| Autoryzacja |Kod autoryzacji jest automatycznie pobierany po kliknięciu przycisku **Autoryzuj** znajdujący się w edytorze fabryki danych i kończenie logowania OAuth. |Tak |
| subscriptionId |Identyfikator subskrypcji platformy Azure |Nie (Jeśli nie zostanie określony, używany subskrypcji usługi data factory). |
| resourceGroupName |Nazwa grupy zasobów platformy Azure |Nie (Jeśli nie zostanie określony, używany grupy zasobów usługi data factory). |
| sessionId |Identyfikator sesji z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używana tylko raz. Korzystając z edytora fabryki danych, ten identyfikator jest generowany automatycznie. |Tak |


#### <a name="json-example"></a>Przykład kodu JSON
W poniższym przykładzie przedstawiono definicję formatu JSON dla usługi połączonej usługi Azure Data Lake Analytics.

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
Tworzenie połączonej usługi Azure SQL i używać ich z [działania dotyczącego procedury składowanej](#stored-procedure-activity) aby wywołać procedurę składowaną z potoku usługi fabryka danych.

### <a name="linked-service"></a>Połączona usługa
Do usługi Azure SQL Database do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDatabase**, a następnie określ następujące właściwości w **typeProperties** sekcja:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| connectionString |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Database dla właściwości connectionString. |Tak |

#### <a name="json-example"></a>Przykład kodu JSON

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

Zobacz [Łącznik usług SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties) szczegółowe informacje dotyczące tej połączonej usługi.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Tworzenie usługi Azure SQL Data Warehouse połączone i używać ich z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych.

### <a name="linked-service"></a>Połączona usługa
Do usługi Azure SQL Data Warehouse do definiowania połączonej usługi, ustaw **typu** połączonej usługi, aby **AzureSqlDW**, a następnie określ następujące właściwości w **typeProperties** sekcja:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| connectionString |Podaj informacje wymagane do połączenia z wystąpieniem usługi Azure SQL Data Warehouse dla właściwości connectionString. |Tak |

#### <a name="json-example"></a>Przykład kodu JSON

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
Tworzenie usługi połączonej programu SQL Server i używać ich z [działania dotyczącego procedury składowanej](data-factory-stored-proc-activity.md) aby wywołać procedurę składowaną z potoku usługi fabryka danych.

### <a name="linked-service"></a>Połączona usługa
Tworzenie połączonej usługi typu **OnPremisesSqlServer** połączyć z lokalnej bazy danych programu SQL Server z fabryką danych. Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla lokalnej połączonej usługi SQL Server.

Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla połączonej usługi SQL Server.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Właściwość type powinna być równa: **OnPremisesSqlServer**. |Tak |
| connectionString |Określ informacje o parametry połączenia potrzebne do połączenia z bazą danych programu SQL Server w środowisku lokalnym przy użyciu uwierzytelniania SQL lub uwierzytelniania Windows. |Tak |
| gatewayName |Nazwa bramy, do którego usługa Data Factory powinna używać do łączenia z bazą danych programu SQL Server w środowisku lokalnym. |Tak |
| username |Określ nazwę użytkownika, jeśli korzystasz z uwierzytelniania Windows. Przykład: **nazwa_domeny\\username**. |Nie |
| password |Określ hasło dla konta użytkownika, która została określona jako nazwy użytkownika. |Nie |

Umożliwia ona szyfrowanie poświadczeń za pomocą **New AzDataFactoryEncryptValue** polecenia cmdlet i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (**EncryptedCredential** właściwość):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Przykład: Kod JSON przy użyciu uwierzytelniania SQL

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
#### <a name="example-json-for-using-windows-authentication"></a>Przykład: Plik JSON do korzystania z uwierzytelniania Windows

Jeśli nie określono nazwy użytkownika i hasła, brama używa ich do personifikacji konta określonego użytkownika, aby nawiązać połączenie z lokalną bazą danych programu SQL Server. W przeciwnym razie brama nawiązuje połączenie z serwerem SQL bezpośrednio za pomocą kontekstu zabezpieczeń bramy (jego konta uruchamiania).

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

Aby uzyskać więcej informacji, zobacz [łącznik programu SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artykułu.

## <a name="data-transformation-activities"></a>DZIAŁANIA PRZEKSZTAŁCANIA DANYCH

Działanie | Opis
-------- | -----------
[Działanie HDInsight Hive](#hdinsight-hive-activity) | Działanie HDInsight Hive w potoku usługi fabryka danych wykonuje zapytania programu Hive na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie.
[Działanie HDInsight Pig](#hdinsight-pig-activity) | Działanie HDInsight Pig w potoku usługi fabryka danych wykonuje zapytania Pig na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie.
[Działania technologii MapReduce w usłudze HDInsight](#hdinsight-mapreduce-activity) | Działanie HDInsight MapReduce w potoku usługi fabryka danych wykonuje programów MapReduce na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie.
[Działania przesyłania strumieniowego usługi HDInsight](#hdinsight-streaming-activity) | Działania przesyłania strumieniowego HDInsight w potoku usługi fabryka danych wykonuje programy przesyłania strumieniowego usługi Hadoop na własną rękę lub klastra Windows/Linux-based HDInsight na żądanie.
[Działania platformy Spark w usłudze HDInsight](#hdinsight-spark-activity) | Działanie HDInsight Spark w potoku usługi fabryka danych wykonuje programów platformy Spark w klastrze HDInsight.
[Działanie wykonywania wsadowego w usłudze Machine Learning](#machine-learning-batch-execution-activity) | Usługa Azure Data Factory umożliwia łatwe tworzenie potoków korzystających z opublikowanej usługi sieci web Azure Machine Learning do analizy predykcyjnej. Przy użyciu działanie wykonywania wsadowego w potoku usługi fabryka danych Azure, możesz wywołać usługę internetową Machine Learning w celu prognozowania na danych w usłudze batch.
[Działania aktualizowania zasobów w usłudze Machine Learning](#machine-learning-update-resource-activity) | Wraz z upływem czasu modele predykcyjne w usłudze Machine Learning do oceny eksperymentów konieczne retrained, przy użyciu nowych danych wejściowych zestawów danych. Po zakończeniu ponownego trenowania, chcesz zaktualizować usługi internetowej przyznawania ocen retrained modelu uczenia maszynowego. Działanie aktualizacji zasobu służy do aktualizowania usługi sieci web przy użyciu nowo trenowanego modelu.
[Działania procedur składowanych](#stored-procedure-activity) | Działanie procedury składowanej w potoku usługi fabryka danych umożliwia wywoływanie procedury składowanej w jednym z następujących magazynów danych: Azure SQL Database, Azure SQL Data Warehouse, bazy danych SQL Server w przedsiębiorstwie lub Maszynie wirtualnej platformy Azure.
[Działania usługi Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) | Działania języka U-SQL usługi Data Lake Analytics uruchamia skrypt U-SQL w klastrze usługi Azure Data Lake Analytics.
[Niestandardowe działanie platformy .NET](#net-custom-activity) | Jeśli zachodzi potrzeba Przekształcanie danych w taki sposób, że nie jest obsługiwana przez usługę Data Factory, możesz utworzyć niestandardowe działanie za pomocą własnej logiki przetwarzania danych i użyć działania w potoku. Można skonfigurować niestandardowe działanie platformy .NET do uruchamiania przy użyciu usługi Azure Batch lub klaster usługi HDInsight na platformie Azure.


## <a name="hdinsight-hive-activity"></a>Działania technologii Hive w usłudze HDInsight
Można określić następujące właściwości w definicji JSON działania programu Hive. Właściwość type dla działania muszą być: **HDInsightHive**. Należy najpierw utworzyć usługi połączonej HDInsight i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightHive:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| skrypt |Określ wbudowany skrypt programu Hive |Nie |
| Ścieżka skryptu |Store skryptu programu Hive w usłudze Azure blob storage i podaj ścieżkę do pliku. Użyj właściwości 'obszarami script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary klucz/wartość do odwoływania się do skryptu programu Hive przy użyciu "hiveconf" |Nie |

Te właściwości typu są specyficzne dla działania programu Hive. Inne właściwości (poza sekcji typeProperties) są obsługiwane dla wszystkich działań.

### <a name="json-example"></a>Przykład kodu JSON
Następujący kod JSON definiuje działanie HDInsight Hive w potoku.

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

Aby uzyskać więcej informacji, zobacz [działania programu Hive](data-factory-hive-activity.md) artykułu.

## <a name="hdinsight-pig-activity"></a>Działania technologii Pig w usłudze HDInsight
Można określić następujące właściwości w definicji JSON działania technologii Pig. Właściwość type dla działania muszą być: **HDInsightPig**. Należy najpierw utworzyć usługi połączonej HDInsight i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightPig:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| skrypt |Określ wbudowany skrypt Pig |Nie |
| Ścieżka skryptu |Store skrypt programu Pig w usłudze Azure blob storage i podaj ścieżkę do pliku. Użyj właściwości 'obszarami script' lub "scriptPath". Nie można używać razem. Nazwa pliku jest rozróżniana wielkość liter. |Nie |
| Definiuje |Określ parametry jako pary klucz/wartość do odwoływania się do skryptu języka Pig |Nie |

Te właściwości typu są specyficzne dla działania technologii Pig. Inne właściwości (poza sekcji typeProperties) są obsługiwane dla wszystkich działań.

### <a name="json-example"></a>Przykład kodu JSON

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

Aby uzyskać więcej informacji, zobacz działania technologii Pig w artykule.

## <a name="hdinsight-mapreduce-activity"></a>Działania technologii MapReduce w usłudze HDInsight
Można określić następujące właściwości w definicji JSON działania MapReduce. Właściwość type dla działania muszą być: **HDInsightMapReduce**. Należy najpierw utworzyć usługi połączonej HDInsight i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightMapReduce:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| jarLinkedService | Nazwa połączonej usługi dla usługi Azure Storage, który zawiera plik JAR. | Tak |
| jarFilePath | Ścieżka do pliku JAR w usłudze Azure Storage. | Tak |
| className | Nazwa główna klasa w pliku JAR. | Tak |
| argumenty | Lista rozdzielonych przecinkami argumenty programu MapReduce. W czasie wykonywania, widzisz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty z argumentami MapReduce, należy wziąć pod uwagę przy użyciu opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe, — itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości) | Nie |

### <a name="json-example"></a>Przykład kodu JSON

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

Aby uzyskać więcej informacji, zobacz [działania technologii MapReduce](data-factory-map-reduce.md) artykułu.

## <a name="hdinsight-streaming-activity"></a>Działania przesyłania strumieniowego usługi HDInsight
Można określić następujące właściwości w definicji JSON działania przesyłania strumieniowego usługi Hadoop. Właściwość type dla działania muszą być: **HDInsightStreaming**. Należy najpierw utworzyć usługi połączonej HDInsight i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightStreaming:

| Właściwość | Opis |
| --- | --- |
| mapowania | Nazwa pliku wykonywalnego mapowania. W tym przykładzie cat.exe jest mapowania pliku wykonywalnego.|
| Reduktor | Nazwa pliku wykonywalnego reduktor. W tym przykładzie wc.exe jest reduktor pliku wykonywalnego. |
| wejście | Plik wejściowy (w tym miejscu) dla mapowania. W przykładzie: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample jest kontener obiektów blob, przykład/data/Gutenberg to folder, a davinci.txt jest obiekt blob. |
| wyjście | Plik wyjściowy (w tym miejscu) dla reduktor. Dane wyjściowe zadania przesyłania strumieniowego usługi Hadoop jest zapisywany w lokalizacji określonej dla tej właściwości. |
| filePaths | Ścieżki dla plików wykonywalnych mapowania i reduktor. W przykładzie: "adfsample/example/apps/wc.exe" adfsample jest kontener obiektów blob, przykładowe/aplikacje jest folder i wc.exe jest plik wykonywalny. |
| fileLinkedService | Połączona usługa Azure Storage reprezentujący magazynu platformy Azure, który zawiera pliki określone w sekcji filePaths. |
| argumenty | Lista rozdzielonych przecinkami argumenty programu MapReduce. W czasie wykonywania, widzisz kilka dodatkowych argumentów (na przykład: mapreduce.job.tags) z platformy MapReduce. Aby odróżnić argumenty z argumentami MapReduce, należy wziąć pod uwagę przy użyciu opcji i wartości jako argumenty, jak pokazano w poniższym przykładzie (- s, — dane wejściowe, — itp., dane wyjściowe są opcje bezpośrednio następuje ich wartości) |
| getDebugInfo | Opcjonalny element. Jeśli ustawiono błędów, dzienniki są pobierane tylko w przypadku niepowodzenia. Po jego ustawieniu wszystkich dzienników będą pobierane zawsze niezależnie od stanu wykonywania. |

> [!NOTE]
> Wyjściowy zestaw danych należy określić dla działania przesyłania strumieniowego usługi Hadoop dla **generuje** właściwości. Ten zestaw danych może być po prostu fikcyjnego zestawu danych, który jest wymagany do harmonogramu potoku (co godzinę, codziennie itp.). Działanie nie przyjmuje dane wejściowe, można pominąć, określając wejściowy zestaw danych działania dla **dane wejściowe** właściwości.

## <a name="json-example"></a>Przykład kodu JSON

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
Można określić następujące właściwości w definicji JSON działania platformy Spark. Właściwość type dla działania muszą być: **HDInsightSpark**. Należy najpierw utworzyć usługi połączonej HDInsight i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania HDInsightSpark:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| rootPath | Kontener obiektów Blob platformy Azure i folder zawierający plik platformy Spark. Nazwa pliku jest rozróżniana wielkość liter. | Tak |
| entryFilePath | Względna ścieżka do folderu głównego kodu lub pakietu platformy Spark. | Tak |
| className | Główna klasa platformy Java lub Spark aplikacji | Nie |
| argumenty | Lista argumentów wiersza polecenia do programu platformy Spark. | Nie |
| proxyUser | Konto użytkownika do personifikacji, aby wykonać program platformy Spark | Nie |
| sparkConfig | Właściwości konfiguracji aparatu Spark. | Nie |
| getDebugInfo | Określa, kiedy Spark pliki dziennika są kopiowane do usługi Azure storage używany przez klaster HDInsight (lub) określony przez sparkJobLinkedService. Dozwolone wartości: Brak zawsze lub niepowodzenie. Wartość domyślna: Brak. | Nie |
| sparkJobLinkedService | Usługi Azure Storage połączone usługi, która ma platformy Spark, zależności, dzienniki i plik zadania.  Jeśli nie określisz wartości dla tej właściwości, jest używany Magazyn skojarzony z klastrem HDInsight. | Nie |

### <a name="json-example"></a>Przykład kodu JSON

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
- **RootPath** ustawiono **adfspark\\pyFiles** gdzie adfspark to kontener obiektów Blob platformy Azure i pyFiles jest dobrym rozwiązaniem folder, w tym kontenerze. W tym przykładzie usługi Azure Blob Storage jest ten, który jest skojarzony z klastrem Spark. Możesz przekazać plik do innej usługi Azure Storage. Jeśli tak zrobisz, Utwórz połączoną usługę Azure Storage połączyć tego konta magazynu z fabryką danych. Następnie określ nazwy połączonej usługi, jako wartość **sparkJobLinkedService** właściwości. Zobacz właściwości działania platformy Spark, szczegółowe informacje na temat tej właściwości i inne właściwości, obsługiwanych przez działanie platformy Spark.
- **Właściwość entryFilePath** ustawiono **test.py**, czyli pliku python.
- **Getdebuginfo —** właściwość jest ustawiona na **zawsze**, co oznacza, że pliki dziennika są zawsze generowany (powodzenie lub niepowodzenie).

    > [!IMPORTANT]
    > Zaleca się, że nie ustawisz tej właściwości zawsze w środowisku produkcyjnym, chyba że są rozwiązywania problemów.
- **Generuje** sekcja zawiera jeden wyjściowy zestaw danych. Należy określić wyjściowy zestaw danych, nawet jeśli program platformy spark nie generuje żadnych danych wyjściowych. Wyjściowy zestaw danych kieruje harmonogramem potoku (co godzinę, codziennie itp.).

Aby uzyskać więcej informacji na temat działania, zobacz [działania platformy Spark](data-factory-spark.md) artykułu.

## <a name="machine-learning-batch-execution-activity"></a>Działanie wykonywania wsadowego w usłudze Machine Learning
W usłudze Azure Machine Learning studio definicji JSON działania wykonywania wsadowego, można określić następujące właściwości. Właściwość type dla działania muszą być: **AzureMLBatchExecution**. Musi utworzyć Azure Machine Learning najpierw połączonej usługi i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania AzureMLBatchExecution:

Właściwość | Opis | Wymagane
-------- | ----------- | --------
webServiceInput | Zestaw danych, które zostaną przekazane jako dane wejściowe dla usługi sieci web Azure Machine Learning studio. Ten zestaw danych, również muszą być zawarte w danych wejściowych na potrzeby działania. |Użyj webServiceInput i webServiceInputs. |
webServiceInputs | Określ zestawy danych, które zostaną przekazane jako dane wejściowe dla usługi sieci web Azure Machine Learning studio. Jeśli usługa sieci web wymaga wielu danych wejściowych, należy użyć właściwości webServiceInputs zamiast przy użyciu właściwości webServiceInput. Zestawy danych, które są przywoływane przez **webServiceInputs** muszą także być ujęte w działaniu **dane wejściowe**. | Użyj webServiceInput i webServiceInputs. |
webServiceOutputs | Zestawy danych, które są przypisane jako dane wyjściowe dla usługi sieci web Azure Machine Learning studio. Usługa sieci web zwraca dane wyjściowe, w tym zestawie danych. | Tak |
globalParameters | Określ wartości dla parametrów usługi sieci web w tej sekcji. | Nie |

### <a name="json-example"></a>Przykład kodu JSON
W tym przykładzie działanie ma zestaw danych **MLSqlInput** jako dane wejściowe i **MLSqlOutput** jako dane wyjściowe. **MLSqlInput** jest przekazywany jako dane wejściowe do usługi sieci web, za pomocą **webServiceInput** właściwość JSON. **MLSqlOutput** jest przekazywany jako dane wyjściowe do usługi sieci Web, za pomocą **webServiceOutputs** właściwość JSON.

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

W tym przykładzie JSON wdrożonej usługi Azure Machine Learning w sieci Web używa modułu zapisywania i odczytywania danych z usługi Azure SQL Database i do odczytu/zapisu. Ta usługa sieci Web udostępnia następujące cztery parametry:  Nazwa serwera bazy danych, nazwę bazy danych, nazwę konta użytkownika serwera i hasło konta użytkownika serwera.

> [!NOTE]
> Tylko dane wejściowe i wyjściowe aktywności AzureMLBatchExecution mogą być przekazywane jako parametry do usługi sieci Web. Na przykład w powyższym fragmencie kodu JSON MLSqlInput jest wartością wejściową działania AzureMLBatchExecution, który jest przekazywany jako dane wejściowe do usługi sieci Web za pomocą parametru webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Działanie aktualizowania zasobów w usłudze Machine Learning
Można określić następujące właściwości w usłudze Azure Machine Learning studio definicji JSON działania zasobów aktualizacji. Właściwość type dla działania muszą być: **AzureMLUpdateResource**. Musi utworzyć Azure Machine Learning najpierw połączonej usługi i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania AzureMLUpdateResource:

Właściwość | Opis | Wymagane
-------- | ----------- | --------
trainedModelName | Nazwa retrained modelu. | Tak |
trainedModelDatasetName | Zestaw danych wskazuje plik iLearner, zwrócony przez operację ponownego trenowania. | Tak |

### <a name="json-example"></a>Przykład kodu JSON
Potok ma dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Azure Machine Learning studio Batch Execution, działanie trwa danych szkoleniowych jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Działania wywołuje usługę sieci web szkolenia (udostępniane jako usługi sieci web eksperymentu szkolenia) przy użyciu danych wejściowych szkolenia i odbiera plik ilearner z usługi sieci Web. PlaceholderBlob jest po prostu fikcyjne dane wyjściowe zestawu danych, który jest wymagany przez usługę Azure Data Factory, aby uruchomić potok.


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
Można określić następujące właściwości w definicji JSON działania języka U-SQL. Właściwość type dla działania muszą być: **DataLakeAnalyticsU-SQL**. Należy utworzyć usługę połączoną usługi Azure Data Lake Analytics i określ nazwę ją jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania DataLakeAnalyticsU SQL:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| scriptPath |Ścieżka do folderu zawierającego skrypt U-SQL. Nazwa pliku jest uwzględniana wielkość liter. |Nie (Jeśli używany jest skrypt) |
| scriptLinkedService |Połączonej usługi, która łączy magazyn, który zawiera skrypt w usłudze data factory |Nie (Jeśli używany jest skrypt) |
| skrypt |Określ zamiast określania scriptPath i element scriptLinkedService wykonanie wbudowanego skryptu. Na przykład: "skrypt": "Testowanie tworzenie bazy danych". |Nie (Jeśli używasz scriptPath i element scriptLinkedService) |
| degreeOfParallelism |Maksymalna liczba węzłów równocześnie używane do uruchamiania zadania. |Nie |
| priorytet |Określa, które spośród wszystkich, które są umieszczane w kolejce zadań, należy wybrać do uruchomienia jako pierwsza. Im mniejsza liczba, tym wyższy priorytet. |Nie |
| parameters |Parametry skryptu U-SQL |Nie |

### <a name="json-example"></a>Przykład kodu JSON

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

Aby uzyskać więcej informacji, zobacz [działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Działania procedur składowanych
Można określić następujące właściwości w definicji przechowywane procedury Format JSON działania. Właściwość type dla działania muszą być: **SqlServerStoredProcedure**. Musisz utworzyć jeden z następujących usług połączonych i określić nazwy połączonej usługi, jako wartość **linkedServiceName** właściwości:

- Oprogramowanie SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania SqlServerStoredProcedure:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| storedProcedureName |Określ nazwę procedury składowanej w usłudze Azure SQL database lub Azure SQL Data Warehouse, który jest reprezentowany przez połączoną usługę, która używa tabeli wyjściowej. |Tak |
| storedProcedureParameters |Określ wartości dla parametrów procedury składowanej. Jeśli musisz przekazać wartości null dla parametru należy użyć składni: "param1": wartość null (wszystkie małe litery). Zobacz poniższy przykład, aby dowiedzieć się więcej o korzystaniu z tej właściwości. |Nie |

Jeśli określisz wejściowy zestaw danych musi być dostępny (w stanie "Gotowy") dla działania procedury składowanej do uruchomienia. Wejściowy zestaw danych nie mogą być używane w procedurze składowanej jako parametr. Tylko służy do sprawdzania zależności przed rozpoczęciem działania procedury składowanej. Należy określić wyjściowy zestaw danych działania procedura składowana.

Wyjściowy zestaw danych określa **harmonogram** działania procedury składowanej (co godzinę, co tydzień, co miesiąc, itp.). Wyjściowy zestaw danych należy użyć **połączoną usługę** odwołujący się do usługi Azure SQL Database, Azure SQL Data Warehouse lub bazy danych programu SQL Server ma procedurę przechowywaną, aby uruchomić. Wyjściowy zestaw danych może służyć jako sposób przekazać wyników procedury składowanej do późniejszego przetwarzania przez innego działania ([tworzenie łańcuchów działań](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) w potoku. Jednak fabryka danych nie automatycznie zapisuje dane wyjściowe procedury składowanej do tego zestawu danych. Jest procedury przechowywanej, która zapisuje do tabeli SQL, która wskazuje wyjściowy zestaw danych. W niektórych przypadkach może być wyjściowy zestaw danych **fikcyjnego dataset**, która jest używana tylko po to, aby określić harmonogram uruchamiania działania procedury składowanej.

### <a name="json-example"></a>Przykład kodu JSON

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
Można określić następujące właściwości w definicji JSON działania niestandardowe platformy .NET. Właściwość type dla działania muszą być: **DotNetActivity**. Należy utworzyć usługi Azure HDInsight, połączone lub usługi Azure Batch, połączone usługi i określ nazwę połączonej usługi, jako wartość **linkedServiceName** właściwości. Następujące właściwości są obsługiwane w **typeProperties** sekcji, gdy wartość typu działania DotNetActivity:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| AssemblyName | Nazwa zestawu. W tym przykładzie jest: **MyDotnetActivity.dll**. | Tak |
| EntryPoint |Nazwa klasy, która implementuje interfejsu IDotNetActivity. W tym przykładzie jest: **MyDotNetActivityNS.MyDotNetActivity** gdzie MyDotNetActivityNS jest przestrzenią nazw i MyDotNetActivity jest klasą.  | Tak |
| PackageLinkedService | Nazwa połączonej usługi Azure Storage, który wskazuje na magazynu obiektów blob, który zawiera plik zip działania niestandardowego. W tym przykładzie jest: **AzureStorageLinkedService**.| Tak |
| PackageFile | Nazwa pliku zip. W tym przykładzie jest: **customactivitycontainer/MyDotNetActivity.zip**. | Tak |
| extendedProperties | Rozszerzone właściwości, które można definiować i przekazania kodu platformy .NET. W tym przykładzie **SliceStart** zmienna jest ustawiona na wartość opartą na zmiennej systemowej SliceStart. | Nie |

### <a name="json-example"></a>Przykład kodu JSON

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

Aby uzyskać szczegółowe informacje, zobacz [korzystanie z działań niestandardowych w usłudze Data Factory](data-factory-use-custom-activities.md) artykułu.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące samouczki:

- [Samouczek: tworzenie potoku z działaniem kopiowania](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Samouczek: tworzenie potoku za pomocą działania hive](data-factory-build-your-first-pipeline-using-editor.md)
